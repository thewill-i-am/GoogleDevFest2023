# README - Real-Time Video Streaming with Kafka, OpenCV, and Python

<p align="center">
  <img width="400" height="200" src="https://cdn-images-1.medium.com/max/600/1*iUWxneAQ_kozzLPkFsrakw.png">
</p>

This README combines instructions from two similar projects. It guides you through installing and configuring **Apache Kafka**, **ZooKeeper**, and **Java**, then setting up a Python environment to capture and process video frames in real time with **OpenCV**—sending them through Kafka—and finally consuming them via a **Flask** server.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Requirements](#requirements)
3. [System Update (Ubuntu)](#system-update-ubuntu)
4. [Install Java](#install-java)
5. [Install ZooKeeper](#install-zookeeper)
6. [Install and Start Kafka](#install-and-start-kafka)
7. [Create a Kafka Topic](#create-a-kafka-topic)
8. [Set Up Python Environment](#set-up-python-environment)
9. [Project Structure](#project-structure)
10. [Running the Producer and Consumer](#running-the-producer-and-consumer)
11. [Accessing the Video Stream](#accessing-the-video-stream)
12. [Technical Details](#technical-details)
13. [Credits](#credits)

---

## 2. Requirements

- **Operating System:** Ubuntu (or a similar Linux distribution)
- **Java:** Version 8 or later
- **ZooKeeper**
- **Apache Kafka**
- **Python:** 3.7+ (3.8 or higher recommended)
- **Python Libraries:** OpenCV, Flask, kafka-python, numpy (install via pip)

---

## 3. System Update (Ubuntu)

Before installing any new software, update your Ubuntu system:

```sh
sudo apt-get update -y
sudo apt-get upgrade -y
```

---

## 4. Install Java

Kafka requires Java. If Java is not already installed, add the WebUpd8Team repository and install JDK 8:

```sh
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer -y
```

Verify your Java installation:

```sh
java -version
```

---

## 5. Install ZooKeeper

ZooKeeper is required by Kafka. Install it with:

```sh
sudo apt-get install zookeeperd
```

This will automatically start the ZooKeeper service on the default port (2181).

---

## 6. Install and Start Kafka

Download Kafka (version **2.11-2.1.1**) from the official website:

```sh
wget https://www-eu.apache.org/dist/kafka/2.1.1/kafka_2.11-2.1.1.tgz
```

If this link does not work, download the latest binary from the [Apache Kafka Downloads](https://kafka.apache.org/downloads) page.

Create a directory for Kafka and extract the archive:

```sh
sudo mkdir /opt/Kafka
cd /opt/Kafka
sudo tar -xvf kafka_2.11-2.1.1.tgz -C /opt/Kafka/
```

Start Kafka:

```sh
sudo /opt/Kafka/kafka_2.11-2.1.1/bin/kafka-server-start.sh \
     /opt/Kafka/kafka_2.11-2.1.1/config/server.properties
```

---

## 7. Create a Kafka Topic

For testing purposes, create a sample topic named **"testing"**:

```sh
sudo /opt/Kafka/kafka_2.11-2.1.1/bin/kafka-topics.sh \
     --create --zookeeper localhost:2181 \
     --replication-factor 1 \
     --partitions 1 \
     --topic testing
```

---

## 8. Set Up Python Environment

Inside the project folder, it is recommended to create and activate a virtual environment:

```sh
python3 -m venv venv
source venv/bin/activate  # For Linux/Mac
# .\venv\Scripts\activate   # For Windows
```

Install the required Python libraries:

```sh
pip install -r requirements.txt
```

Ensure your `requirements.txt` includes:
- `opencv-python`
- `kafka-python`
- `flask`
- `numpy`

---

## 9. Project Structure

Below is an example of how your project might be structured:

```
.
├── templates
│   └── index.html               # HTML template for Flask
├── venv                         # Python virtual environment (optional)
├── MobileNetSSD_deploy.caffemodel
├── MobileNetSSD_deploy.prototxt.txt
├── producer.py                  # Captures video, detects objects, sends frames to Kafka
├── consumer.py                  # Flask server that consumes frames from Kafka
├── requirements.txt
└── README.md
```

---

## 10. Running the Producer and Consumer

### Step 1: Start ZooKeeper

If not already running, start ZooKeeper:

```sh
zkserver start
```

### Step 2: Start Kafka

Start Kafka with:

```sh
sudo /opt/Kafka/kafka_2.11-2.1.1/bin/kafka-server-start.sh \
     /opt/Kafka/kafka_2.11-2.1.1/config/server.properties
```

### Step 3: Run the Producer

Run the `producer.py` script to capture video, process frames, and send them to Kafka:

```sh
python producer.py
```

### Step 4: Run the Consumer

Run the `consumer.py` script to start the Flask server that consumes frames from Kafka:

```sh
python consumer.py
```

---

## 11. Accessing the Video Stream

Once the Flask consumer is running, open your web browser and navigate to:

```
http://localhost:8081/
```

You will see a live video feed where bottles appear blurred and other detected objects are highlighted with bounding boxes.

---

## 12. Technical Details

- **OpenCV DNN:** Uses the `MobileNetSSD_deploy.caffemodel` network along with `MobileNetSSD_deploy.prototxt.txt` for object detection.
- **KafkaProducer and KafkaConsumer:** Utilizes the `kafka-python` library.
- **Frame Queue:** The `FrameProducer` (in `producer.py`) detects objects and adds the processed frames to a queue (`Queue`). Frames are then sent to Kafka in JPEG format.
- **Flask Server:** The web server that displays the video. Each frame received from Kafka is sent to the client using `multipart/x-mixed-replace`.

---

## 13. Credits

- [Distributed Video Stream with Python](https://scotch.io/tutorials/build-a-distributed-streaming-system-with-apache-kafka-and-python)
- [Apache Kafka Official Documentation](https://kafka.apache.org/documentation/)
- [OpenCV Official Documentation](https://docs.opencv.org/)
