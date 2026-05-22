# Real-Time Streaming with Apache Kafka
**ENGR 5785G — Assignment 1**

## Overview
A real-time streaming pipeline that reads rows from the UCI Bike Sharing dataset, streams them through Apache Kafka at ~1 row/second, applies a pre-trained Random Forest model for rental count prediction, and publishes results to an output topic — all running simultaneously across three terminals.

---

## Dataset
**UCI Bike Sharing Dataset (hour.csv)**  
Source: https://archive.ics.uci.edu/dataset/275/bike+sharing+dataset  
ML Task: Predict hourly bike rental count (`cnt`) using weather, time, and season features.

---

## Streams Library
**Python + Faust** (`faust-streaming`)  
Faust is the Python equivalent of Kafka Streams. It uses `@app.agent()` decorators to define asynchronous stream-processing agents that consume from one Kafka topic and produce to another — with no manual consumer loop.

---

## ML Model Performance
| Metric | Value |
|--------|-------|
| Model  | Random Forest Regressor (100 trees) |
| MAE    | *28.5* |
| R²     | *0.9421* |
| F1 (bucketed low/med/high) | *0.9103* |
| Features used | season, yr, mnth, hr, holiday, weekday, workingday, weathersit, temp, atemp, hum, windspeed |

> Run `train_model.ipynb` to get your exact numbers and paste them above.

---

## Project Structure
```
kafka-realtime-streaming/
├── train_model.ipynb        # Train & save the ML model (run once)
├── producer.ipynb           # Terminal 3 — sends rows to raw-data topic
├── streams_processor.ipynb  # Terminal 1 — Faust agent, runs ML inference
├── output_consumer.ipynb    # Terminal 2 — prints predictions live
├── bike_model.joblib        # Saved model artifact
├── requirements.txt         # Python dependencies
└── README.md
```

---

## Setup Steps

### 1. Confluent Cloud
1. Sign up free at https://confluent.cloud
2. Create a cluster (Basic tier, any region)
3. Create two topics: `raw-data` and `predictions`
4. Create an API Key (Global access) — save the Key and Secret
5. Copy your Bootstrap Server from Cluster Settings → Endpoints

### 2. Dataset
1. Download `hour.csv` from https://archive.ics.uci.edu/dataset/275
2. Upload to Google Drive at: `MyDrive/kafka-assignment/hour.csv`

### 3. Train the Model
1. Upload `train_model.ipynb` to Google Colab
2. Run all cells — saves `bike_model.joblib` to Drive
3. Note the MAE, R², and F1 scores printed at the end

### 4. Fill in Credentials
In **all three** notebooks, replace:
```python
BOOTSTRAP_SERVERS = 'pkc-XXXXX...:9092'
SASL_USERNAME     = 'YOUR_API_KEY'
SASL_PASSWORD     = 'YOUR_API_SECRET'
```

---

## How to Run (Three Terminals)

Open three separate Google Colab tabs and run in this order:

| Order | File | What it does |
|-------|------|--------------|
| 1st | `streams_processor.ipynb` | Starts Faust worker — waits for messages |
| 2nd | `output_consumer.ipynb`   | Starts consumer — waits for predictions |
| 3rd | `producer.ipynb`          | Starts sending 1 row/second |

Once the producer starts, you will see predictions printing live in the consumer tab.

---

## Video Demo
*https://drive.google.com/file/d/1dxeCVQQkHPv86F2YFNyKgNS63dowgjkn/view?usp=share_link*

---

## Dependencies
```
confluent-kafka==2.3.0
faust-streaming==0.10.14
scikit-learn==1.4.2
joblib==1.3.2
pandas==2.2.1
numpy==1.26.4
```
