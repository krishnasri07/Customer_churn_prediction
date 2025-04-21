
# 6. Cluster Setup Documentation

## Technologies Used

| Technology    | Version       | Purpose                                        |
|---------------|---------------|------------------------------------------------|
| Hadoop        | 3.3.6         | Distributed storage and processing framework   |
| Apache Spark  | 3.4.1         | In-memory distributed data processing engine   |
| Apache Kafka  | 3.5.0         | Real-time streaming platform                   |
| OpenJDK       | 8             | Java runtime for Hadoop and Spark              |
| Ubuntu (VM)   | 20.04         | Operating system on virtual machines           |
| SSH           | Built-in      | Secure node communication                      |
| Jupyter       | 7.3.3         | Web-based interactive data analysis & visualization tool |


---

## Cluster Configuration Details

- **Nodes**: 2 (1 Master, 1 Slave)
- **Master Node IP**: `192.xx.xx.xx`
- **Slave Node IP**: `192.xx.xx.xx`
- **Communication between master and slave** 
- **Hadoop Path**: `/usr/local/hadoop`
- **Spark Path**: `/usr/local/spark`
- **Kafka Path**: `/usr/local/kafka`

---

## Steps to Set Up the Cluster
### 1. IP Settings

### Get VM IPs
```bash
ip a
```

### Add IPs to `/etc/hosts` on both machines
```bash
sudo nano /etc/hosts
```
Add:
```
master 192.168.x.x
slave1 192.168.x.x
```

Replace `192.168.x.x` with actual IPs of the master and slave.

---

### 2. SSH Configuration

### Install SSH
```bash
sudo apt update && sudo apt install openssh-server -y
```

### Generate SSH Key (on both nodes)
```bash
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub
```

### Exchange Keys
- Paste Master key in Slave's `~/.ssh/authorized_keys`
- Paste Slave key in Master's `~/.ssh/authorized_keys`

### Test SSH
```bash
ssh master
ssh slave1
```

---

### 3. Java Installation
```bash
sudo apt update
sudo apt install openjdk-8-jdk -y
java -version
```

---

### 4. Hadoop Setup

### Download and Install Hadoop 3.3.6
```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
tar -xvzf hadoop-3.3.6.tar.gz
sudo mv hadoop-3.3.6 /usr/local/hadoop
```

### Configure Bash
```bash
nano ~/.bashrc
```
Append:
```bash
export HADOOP_HOME=/usr/local/hadoop
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
Apply:
```bash
source ~/.bashrc
```

#### Set Environment Variables:
```bash
export HADOOP_HOME=/usr/local/hadoop
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

#### Hadoop Configurations:
- core-site.xml:
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value>
  </property>
</configuration>
```
- hdfs-site.xml:
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
</configuration>
```
- yarn-site.xml:
```xml
<configuration>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>master</value>
  </property>
</configuration>
```
- slaves:
```
slave
```

#### Format and Start Hadoop:
```bash
hdfs namenode -format
start-dfs.sh
start-yarn.sh
```

---

### 5. Spark Setup

#### Download and Install:
```bash
wget https://downloads.apache.org/spark/spark-3.4.1/spark-3.4.1-bin-hadoop3.tgz
tar -xzf spark-3.4.1-bin-hadoop3.tgz
sudo mv spark-3.4.1-bin-hadoop3 /usr/local/spark
```

#### Environment Variables:
```bash
export SPARK_HOME=/usr/local/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
```

#### spark-env.sh:
```bash
export SPARK_MASTER_HOST=master
```

#### slaves file:
```
slave
```

#### Start Spark:
```bash
start-master.sh
start-slaves.sh
```

---

### 6. Kafka Setup

#### Download and Install:
```bash
wget https://downloads.apache.org/kafka/3.5.0/kafka_2.13-3.5.0.tgz
tar -xzf kafka_2.13-3.5.0.tgz
sudo mv kafka_2.13-3.5.0 /usr/local/kafka
```

#### Start Zookeeper and Kafka:
```bash
cd /usr/local/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties
```
Open another terminal:
```bash
cd /usr/local/kafka
bin/kafka-server-start.sh config/server.properties
```

### 7. Kafka Python Producer

**`producer.py`**
```python
import pandas as pd
import json
from kafka import KafkaProducer
import time

KAFKA_TOPIC = "churn-prediction-input"
KAFKA_SERVER = "localhost:9092"

producer = KafkaProducer(
    bootstrap_servers=KAFKA_SERVER,
    value_serializer=lambda v: json.dumps(v).encode("utf-8"),
)

df = pd.read_csv("/home/pavan/Downloads/Customer_Churn.csv")
df.columns = df.columns.str.strip().str.replace("  ", "_").str.replace(" ", "_")

features = ["Call_Failure", "Complains", "Subscription_Length", "Charge_Amount",
            "Seconds_of_Use", "Frequency_of_use", "Frequency_of_SMS",
            "Distinct_Called_Numbers", "Age_Group", "Tariff_Plan", "Customer_Value"]

df = df[features]

for _, row in df.iterrows():
    producer.send(KAFKA_TOPIC, row.to_dict())
    time.sleep(1)

producer.flush()
producer.close()
```

---

### 8. Kafka Python Consumer

**`consumer.py`**
```python
import json
from kafka import KafkaConsumer

KAFKA_TOPIC = "churn-prediction-output"
KAFKA_SERVER = "localhost:9092"

consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_SERVER,
    auto_offset_reset="earliest",
    enable_auto_commit=True,
    group_id="churn-consumer-group",
    value_deserializer=lambda v: json.loads(v.decode("utf-8")),
)

for message in consumer:
    print(f"Received Prediction: {message.value}")
```

---

### 9. Kafka Stream Processing with XGBoost

**`stream.py`**
```python
import json
from kafka import KafkaConsumer
import xgboost as xgb
import numpy as np
import joblib

KAFKA_TOPIC = "churn-prediction-input"
KAFKA_SERVER = "localhost:9092"

bst = joblib.load("/home/pavan/Downloads/iranian")
consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_SERVER,
    value_deserializer=lambda v: json.loads(v.decode("utf-8")),
)

features = ["Call_Failure", "Complains", "Subscription_Length", "Charge_Amount",
            "Seconds_of_Use", "Frequency_of_use", "Frequency_of_SMS",
            "Distinct_Called_Numbers", "Age_Group", "Tariff_Plan", "Customer_Value"]

for message in consumer:
    data = message.value
    try:
        values = [data[feature] for feature in features]
        dmatrix = xgb.DMatrix(np.array(values).reshape(1, -1))
        churn_proba = bst.predict(dmatrix)[0]
        churn_prediction = int(churn_proba >= 0.5)
        print(f"Churn Probability: {churn_proba:.4f} | Prediction: {churn_prediction}")
    except KeyError as e:
        print(f"Missing Key: {e}")
```

---

## Issues Encountered and Resolutions

| Issue | Description | Resolution |
|-------|-------------|------------|
| dpkg lock error | Package manager busy | Removed lock file and killed the process |
| SSH prompts password | Key not added properly | Fixed with proper key and permission |
| Hadoop not starting | JAVA_HOME misconfigured | Corrected using `readlink -f $(which java)` |
| VM connectivity issues | IP conflict or DHCP enabled | Set static IP and proper host-only network |
| Kafka port issue | Blocked or used by other process | Allowed via `ufw` and ensured no conflict |
| Spark-Kafka integration error | Missing package | Added with `--packages` in `spark-submit` |

---

## Final Notes

- Hadoop UI: `http://master:9870`
- YARN UI: `http://master:8088`
- Spark UI: `http://master:8080`
- Kafka runs on `localhost:9092`

This completes the full big data + real-time streaming cluster setup.
