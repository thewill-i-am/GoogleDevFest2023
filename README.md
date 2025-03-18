
README - Real-Time Video Streaming with Kafka, OpenCV, and Python

This README combines instructions from two similar projects. It guides you through
installing and configuring Apache Kafka, ZooKeeper, and Java, then setting up a Python
environment to capture and process video frames in real time with OpenCV, sending them
through Kafka, and finally consuming them via a Flask server.

--------------------------------------------------------------------------------
Table of Contents
--------------------------------------------------------------------------------

1. Introduction
2. Requirements
3. System Update (Ubuntu)
4. Install Java
5. Install ZooKeeper
6. Install and Start Kafka
7. Create a Kafka Topic
8. Set Up Python Environment
9. Project Structure
10. Running the Producer and Consumer
11. Accessing the Video Stream
12. Credits

--------------------------------------------------------------------------------
1. Introduction
--------------------------------------------------------------------------------

This project demonstrates a real-time video streaming pipeline using:
- Kafka (for message brokering),
- Python (for the producer/consumer logic),
- OpenCV (for object detection and frame processing),
- Flask (for serving the processed video frames over HTTP).

Objects such as bottles are blurred, while other objects are outlined with bounding
boxes. The processed frames are published to Kafka, then consumed by a Flask app
which streams them to a web page.

--------------------------------------------------------------------------------
2. Requirements
--------------------------------------------------------------------------------

- Ubuntu (or a similar Linux distribution).
- Java (version 8 or later).
- ZooKeeper.
- Apache Kafka.
- Python 3.7+ (3.8 or higher recommended).
- OpenCV, Flask, kafka-python, numpy (installed via pip).

--------------------------------------------------------------------------------
3. System Update (Ubuntu)
--------------------------------------------------------------------------------

It's good practice to update your Ubuntu system before installing anything new:

    sudo apt-get update -y
    sudo apt-get upgrade -y

--------------------------------------------------------------------------------
4. Install Java
--------------------------------------------------------------------------------

Kafka requires Java. If you don't already have Java installed, do so by adding the
WebUpd8Team repository (or installing Java from another source):

    sudo add-apt-repository -y ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer -y

Verify your Java installation:

    java -version

--------------------------------------------------------------------------------
5. Install ZooKeeper
--------------------------------------------------------------------------------

ZooKeeper is required by Kafka:

    sudo apt-get install zookeeperd

This should automatically start the ZooKeeper service on default port 2181.

--------------------------------------------------------------------------------
6. Install and Start Kafka
--------------------------------------------------------------------------------

Download Kafka (version 2.11-2.1.1 or the one you prefer) from the official website:

    wget https://www-eu.apache.org/dist/kafka/2.1.1/kafka_2.11-2.1.1.tgz

If that link does not work, download the latest binary from:
https://kafka.apache.org/downloads

Create a directory for Kafka and extract the archive:

    sudo mkdir /opt/Kafka
    cd /opt/Kafka
    sudo tar -xvf kafka_2.11-2.1.1.tgz -C /opt/Kafka/

Make sure Kafka is running:

    sudo /opt/Kafka/kafka_2.11-2.1.1/bin/kafka-server-start.sh \
         /opt/Kafka/kafka_2.11-2.1.1/config/server.properties

--------------------------------------------------------------------------------
7. Create a Kafka Topic
--------------------------------------------------------------------------------

For testing, create a sample topic named "testing":

    sudo /opt/Kafka/kafka_2.11-2.1.1/bin/kafka-topics.sh \
         --create --zookeeper localhost:2181 \
         --replication-factor 1 \
         --partitions 1 \
         --topic testing

--------------------------------------------------------------------------------
8. Set Up Python Environment
--------------------------------------------------------------------------------

Inside the project folder, you can create and activate a Python virtual environment
(optional but recommended):

    python3 -m venv venv
    source venv/bin/activate  # (Linux/Mac)
    # .\venv\Scripts\activate (Windows)

Then install the required Python libraries from the provided requirements file:

    pip install -r requirements.txt

Make sure the following are included in your requirements:
- opencv-python
- kafka-python
- flask
- numpy

--------------------------------------------------------------------------------
9. Project Structure
--------------------------------------------------------------------------------

Below is an example of how your project structure might look:

    .
    ├── templates
    │   └── index.html               # HTML template for Flask
    ├── venv                        # Virtual environment (optional)
    ├── MobileNetSSD_deploy.caffemodel
    ├── MobileNetSSD_deploy.prototxt.txt
    ├── producer.py                 # Captures video, detects objects, sends frames to Kafka
    ├── consumer.py                 # Flask server that consumes frames from Kafka
    ├── requirements.txt
    └── ...

--------------------------------------------------------------------------------
10. Running the Producer and Consumer
--------------------------------------------------------------------------------

1. **Start ZooKeeper** (if not already running):

       zkserver start

2. **Start Kafka**:

       sudo /opt/Kafka/kafka_2.11-2.1.1/bin/kafka-server-start.sh \
            /opt/Kafka/kafka_2.11-2.1.1/config/server.properties

3. **Run the Producer** (producer.py):
   - This script captures frames from a video file (or camera), processes them with OpenCV,
     and sends them to Kafka.

       python producer.py

4. **Run the Consumer** (consumer.py):
   - This script starts a Flask server, listens on the Kafka topic, and serves the processed
     frames to the client via a streaming endpoint.

       python consumer.py

--------------------------------------------------------------------------------
11. Accessing the Video Stream
--------------------------------------------------------------------------------

Once the consumer (Flask) is running, open a web browser and go to:

    http://localhost:8081/

(or whichever host/port you configured in consumer.py).

You should see the webpage with the live video feed. Bottles will appear blurred,
while other recognized objects are highlighted with bounding boxes.

--------------------------------------------------------------------------------
12. Credits
--------------------------------------------------------------------------------

- [Distributed video stream with python](https://scotch.io/tutorials/build-a-distributed-streaming-system-with-apache-kafka-and-python)
- [Apache Kafka Official Documentation](https://kafka.apache.org/documentation/)
- [OpenCV Official Documentation](https://docs.opencv.org/)

