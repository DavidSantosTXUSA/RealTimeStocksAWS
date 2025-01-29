# **Real-Time Stock Market Data Analysis with AWS & Kafka**

## 📌 **Project Overview**
This project is an **end-to-end real-time stock market data processing pipeline** using **Apache Kafka, AWS services (EC2, S3, Glue, Athena), and Python**. The goal is to **stream, process, store, and analyze stock market data efficiently**.

## ✅ **Features**
-  **Real-time data streaming** using Kafka (Producer & Consumer)  
-  **AWS Integration** (EC2 for Kafka, S3 for data storage, Glue for ETL, and Athena for querying)  
-  **Financial market analysis** using Python and SQL  
-  **Scalable & efficient pipeline** with optimized data ingestion  

## 🛠 **Technology Stack**

### **📌 Programming & Data Processing**
- **Java** (Kafka Producer & Consumer)  
- **Python** (Data transformation, API integration)  
- **SQL** (Athena queries for stock data analysis)  

### **📌 Streaming & Messaging**
- **Apache Kafka** (Real-time stock data ingestion)  

### **📌 Cloud Services (AWS)**
- **EC2** (Kafka server hosting)  
- **S3** (Stock data storage)  
- **Glue** (ETL processing for stock data)  
- **Athena** (SQL queries for analysis)  

# 🚀 **Setup Guide**

##  Prerequisites  
✔ A **computer running Windows, macOS, or Linux**  
✔ An **internet connection**  
✔ A **web browser**  
✔ **Python IDE** (VS Code, Jupyter Notebook)  
✔ **AWS CLI installed**  
✔ Basic **AWS knowledge and coding experience**  

## How to Set Up AWS EC2 and Install Kafka**  

### **Step 1: Launch an EC2 Instance**  
1. Log in to your **AWS Console**.  
2. Click on **Services** (top left corner) → Search for **EC2** → Click **Launch Instance**.  
3. **Name the instance** and keep all settings as default.  
4. Choose an **Amazon Linux 2 AMI (64-bit x86)**.  
5. Choose an instance type (e.g., **t2.micro**, which is free tier eligible).  
6. Configure security settings:  
   - Allow **SSH (port 22)**  
   - Allow **Kafka traffic (port 9092)**  
   - Allow **Zookeeper traffic (port 2181)**  
7. Create a **Key Pair** for SSH access and download it.  
8. Click **Launch Instance**.  

### Step 2: Connect to the EC2 Instance via SSH
```bash
ssh -i "your-key.pem" ec2-user@<your-ec2-public-ip>
```
if you get a permission denied error, just change the keys permission with
```
chmod 400 your-key.pem
```

### Step 3: Install Java
```bash
sudo yum install java-1.8.0-openjdk -y
```

### **Step 4: install Kafka**
```bash
wget https://downloads.apache.org/kafka/3.6.2/kafka_2.12-3.6.2.tgz
tar -xvf kafka_2.12-3.6.2.tgz
cd kafka_2.12-3.6.2
```

### Step 5: Start Zookeeper
```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Step 6: Start Kafka Server
Open a new terminal window.
Increase heap to aviod errors and start kafka
```bash
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
bin/kafka-server-start.sh config/server.properties
```

### Step 7: Change Kafka Listener to Public IP
Open server.properties in nano editor:
```bash
sudo nano config/server.properties
```
Change the line: advertised.listeners=PLAINTEXT://localhost:9092
To: advertised.listeners=PLAINTEXT://<your-ec2-public-ip>:9092

Save & exit (press CTRL+O, then Enter, then CTRL+X).

Restart Kafka
```bash
bin/kafka-server-start.sh config/server.properties
```

### Step 8: Create Kafka Topic
```bash
bin/kafka-topics.sh --create --topic stock_data --bootstrap-server <your-ec2-public-ip>:9092 --replication-factor 1 --partitions 1
```

### Step 9: Start Kafka Producer & Consumer
Producer(Sends stock data):
```bash
bin/kafka-console-producer.sh --topic stock_data --bootstrap-server <your-ec2-public-ip>:9092
```
Consumer(Receives Data)
```bash
bin/kafka-console-consumer.sh --topic stock_data --bootstrap-server <your-ec2-public-ip>:9092
```
### Step 10: Create an S3 Bucket
Go to AWS Console → S3 → Create Bucket.
Name the bucket (e.g., stock-market-data).
Enable versioning (optional) and create the bucket.

### Step 11: Upload Processed Data
Upload indexProcessed.csv using:
```bash
aws s3 cp indexProcessed.csv s3://stock-market-data/
```

### Step 12: Create AWS Glue Crawler
Go to AWS Console → Glue → Crawlers.
Click Create Crawler and name it (StockMarketCrawler).
Add a data source → Select S3 → Choose your bucket.
Set an IAM role with S3 and Glue permissions.
Run the crawler.

### Step 13: Query Data with Athena
Go to AWS Athena → Set an S3 query result location.
Now you can run any queries!
example:
SELECT * FROM stock_data LIMIT 10;
