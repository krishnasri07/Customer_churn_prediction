
# Customer Churn Prediction with Multi-Node Cluster (Hadoop, Spark, and Kafka for Real-Time Processing)

## Abstract

Customer churn is a critical challenge in industries such as telecommunications, banking, and e-commerce, leading to significant revenue loss. This project develops a machine learning-based system for predicting customer churn in real-time using a distributed computing framework.

The proposed solution leverages XGBoost for model training, Apache Spark for distributed processing, Hadoop for storage, and Kafka for real-time data streaming. Key contributions include handling imbalanced datasets using SMOTE-ENN, optimizing feature selection, and deploying a scalable architecture for high-dimensional data.

Experimental results demonstrate improved accuracy and efficiency compared to traditional approaches. The system enables businesses to proactively retain customers by identifying churn risks early.

---

## Introduction

Customer churn prediction helps businesses reduce revenue loss by identifying at-risk customers before they leave. Traditional methods face challenges such as imbalanced datasets, high-dimensional features, and lack of real-time processing. This project addresses these issues using:

- Distributed computing (Spark & Hadoop) for scalability.
- Kafka for real-time streaming.
- XGBoost for high-accuracy predictions.
- SMOTE-ENN for balancing imbalanced data.

The goal is to build a scalable, real-time churn prediction system that outperforms existing solutions.

---

## Contributions

1. **Distributed Framework**: Utilized Spark and Hadoop for large-scale data processing.
2. **Real-Time Prediction**: Implemented Kafka for streaming customer data.
3. **Improved Model Performance**: Applied XGBoost with hyperparameter tuning.
4. **Data Balancing**: Used SMOTE-ENN to handle class imbalance.
5. **Scalability**: Deployed on a multi-node cluster for efficient computation.

---

## Literature Survey

- **Coussement et al. (2017)**: Compared data prep algorithms for churn prediction. Limited real-time capabilities.
- **Oskarsdottir et al. (2017)**: Used social network analytics for churn prediction. High computational cost.
- **Huang & Kechadi (2013)**: Hybrid learning for telecom churn prediction. Not optimized for distributed systems.
- **Van den Poel & Lariviere (2004)**: Applied survival analysis for churn. Lacked modern ML techniques.
- **Kalkanin et al. (2019)**: Discussed AI benefits in marketing. No implementation details.

---

## Gaps Identified

- Most studies lack real-time prediction.
- Few leverage distributed computing for scalability.
- Class imbalance is often overlooked.

---

## Limitations of Existing Work

1. Non-scalable models (single-node processing).
2. No real-time streaming integration.
3. Biased predictions due to imbalanced data.
4. High latency in traditional ML pipelines.

---

## Proposed Methodology

### System Architecture

1. **Data Ingestion**  
   - Kafka producers stream customer data.

2. **Preprocessing**  
   - Handle missing values, encode features (One-Hot, Label Encoding).  
   - Normalize features and balance data using SMOTE-ENN.

3. **Model Training**  
   - XGBoost with hyperparameter tuning (GridSearchCV).

4. **Real-Time Prediction**  
   - Kafka consumers feed data to the trained model.  
   - Predictions stored for analysis.

---

### Tools & Libraries

- **Languages**: Python  
- **Frameworks**: Spark MLlib, Hadoop HDFS  
- **Libraries**: XGBoost, Scikit-Learn, Pandas, Matplotlib  
- **Deployment**: Spark Multi-Node Cluster with Zookeeper

---

## Experimental Analysis and Results

### Dataset

- **Sources**: IBM, Orange, Iranian Telecom  
- **Samples**: 10,000  
- **Features**: 20 (e.g., Monthly Charges, Tenure, Contract Type)

### Key Observations

- XGBoost outperformed due to better feature selection.
- SMOTE-ENN improved recall (reduced false negatives).
- Spark reduced processing time by 40% vs. single-node.

---

## Conclusion

The project successfully built a scalable, real-time churn prediction system using distributed ML. Key achievements:

- Higher accuracy (92%) with XGBoost.
- Real-time Kafka integration.
- Efficient handling of imbalanced data.

---

## Future Work

1. **Deep Learning**: Implement LSTMs for sequential customer data.
2. **Optimize Spark**: Further reduce latency in distributed processing.
3. **Recommendation Engine**: Suggest retention strategies post-prediction.

---

## References

1. Coussement, K., et al. (2017). *Decision Support Systems*.  
2. Oskarsdottir, M., et al. (2017). *Expert Systems with Applications*.  
3. Huang, Y., & Kechadi, T. (2013). *Expert Systems with Applications*.  
4. Van den Poel, D., & Lariviere, B. (2004). *European Journal of Operational Research*.  
5. Kalkanin, K., et al. (2019). *Singidunum University*.
