Here’s the content reformatted into a clear and structured Markdown format:

# Exercise 1: Download and Extract Kafka

1. **Open a New Terminal**
   - Click the menu bar and select **Terminal > New Terminal**.

2. **Download Kafka**
   ```bash
   wget https://downloads.apache.org/kafka/3.8.0/kafka_2.13-3.8.0.tgz
   ```

3. **Extract Kafka**
   ```bash
   tar -xzf kafka_2.13-3.8.0.tgz
   ```
   - This command will create a new directory `kafka_2.13-3.8.0` in the current directory.

---

# Exercise 2: Configure KRaft and Start Server

1. **Navigate to Kafka Directory**
   ```bash
   cd kafka_2.13-3.8.0
   ```

2. **Generate a Cluster UUID**
   ```bash
   KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
   ```

3. **Configure the Log Directories**
   ```bash
   bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties
   ```

4. **Start the Kafka Server**
   ```bash
   bin/kafka-server-start.sh config/kraft/server.properties
   ```
   - You can confirm that the Kafka server has started when you see messages like "Kafka Server started".

---

# Exercise 3: Create a Topic and Start Producer

1. **Open a New Terminal and Navigate to Kafka Directory**
   ```bash
   cd kafka_2.13-3.8.0
   ```

2. **Create a Topic Named `news`**
   ```bash
   bin/kafka-topics.sh --create --topic news --bootstrap-server localhost:9092
   ```
   - You should see the message: `Created topic news`.

3. **Start a Producer**
   ```bash
   bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic news
   ```
   - After the producer starts, type any text message and press enter. You can also send multiple messages like below:
   ```
   Good morning
   Good day
   Enjoy the Kafka lab
   ```

---

# Exercise 4: Start Consumer

1. **Open a New Terminal and Navigate to Kafka Directory**
   ```bash
   cd kafka_2.13-3.8.0
   ```

2. **Listen to Messages in the `news` Topic**
   ```bash
   bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic news --from-beginning
   ```
   - You should see all the messages sent from the producer. You can go back to the producer terminal and type more messages, which will appear here.

---

# Exercise 5: Explore Kafka Directories

1. **Open a New Terminal and Navigate to Kafka Directory**
   ```bash
   cd kafka_2.13-3.8.0
   ```

2. **Explore the Root Directory**
   ```bash
   ls
   ```

3. **Check the Logs for the `news` Topic**
   ```bash
   ls /tmp/kraft-combined-logs/news-0
   ```
   - Note: All messages are stored in the `news-0` directory under the `/tmp/kraft-combined-logs` directory.

---

# Exercise 6: Clean Up

1. **Stop the Producer**
   - In the terminal where you are running the producer, press `CTRL+C`.

2. **Stop the Consumer**
   - In the terminal where you are running the consumer, press `CTRL+C`.

3. **Stop the Kafka Server**
   - In the terminal where you are running the Kafka server, press `CTRL+C`.

---

This Markdown format organizes the exercises clearly and makes it easy to follow along with the steps. Let me know if you need any further adjustments!
