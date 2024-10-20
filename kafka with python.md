Here's the formatted markdown for your exercises:

---

# Exercise 3: Create a Topic in the `admin.py` File

To create a Kafka topic programmatically, you will use the `kafka-python` library.

## Step 1: Install `kafka-python`

Open a new terminal and navigate to the `kafka_2.13-3.8.0` directory:

```bash
cd kafka_2.13-3.8.0
```

Install the `kafka-python` package by running the following command:

```bash
pip3 install kafka-python
```

## Step 2: Create `admin.py`

Create a file named `admin.py`:

```bash
touch admin.py
```

Open `admin.py` in your IDE and paste the following content into the file:

```python
from kafka.admin import KafkaAdminClient, NewTopic

admin_client = KafkaAdminClient(bootstrap_servers="localhost:9092", client_id='test')
topic_list = []
new_topic = NewTopic(name="bankbranch", num_partitions=2, replication_factor=1)
topic_list.append(new_topic)
admin_client.create_topics(new_topics=topic_list)
```

**Note:** This code creates a topic named "bankbranch".

---

# Exercise 4: Create the `producer.py` File

You need a producer to send messages to Kafka.

## Step 1: Create `producer.py`

Create a file named `producer.py`:

```bash
touch producer.py
```

Open `producer.py` in your IDE and paste the following content into the file:

```python
from kafka import KafkaProducer
import json

producer = KafkaProducer(value_serializer=lambda v: json.dumps(v).encode('utf-8'))
producer.send("bankbranch", {'atmid': 1, 'transid': 100})
producer.send("bankbranch", {'atmid': 2, 'transid': 101})
producer.flush()
producer.close()
```

In this code, the producer sends two messages to the `bankbranch` topic.

---

# Exercise 5: Create the `consumer.py` File

You need a consumer to read messages from Kafka.

## Step 1: Create `consumer.py`

Create a file named `consumer.py`:

```bash
touch consumer.py
```

Open `consumer.py` in your IDE and paste the following content into the file:

```python
from kafka import KafkaConsumer

consumer = KafkaConsumer('bankbranch',
                         group_id=None,
                         bootstrap_servers=['localhost:9092'],
                         auto_offset_reset='earliest')

print("Hello")
print(consumer)

for msg in consumer:
    print(msg.value.decode("utf-8"))
```

---

# Exercise 6: Execute the Three Python Files

## Step 1: Run `admin.py` and `producer.py`

Execute `admin.py` and `producer.py` using the following commands in the terminal:

```bash
python3 admin.py
python3 producer.py
```

## Step 2: Run `consumer.py`

Open a new terminal, navigate to the `kafka_2.13-3.8.0` directory, and run:

```bash
cd kafka_2.13-3.8.0
python3 consumer.py
```

Your consumer should print the messages sent by the producer.

---

# Practice Exercise: Create a New Producer

Create a new producer in a file named `new_producer.py` that will take user input continuously and accept input for the ATM number they want to transact with. The code for `new_producer.py` is as follows:

```python
from kafka import KafkaProducer
import json

producer = KafkaProducer(value_serializer=lambda v: json.dumps(v).encode('utf-8'))
transid = 102

while True:
    user_input = input("Do you want to add a transaction? (press 'n' to stop): ")
    if user_input.lower() == 'n':
        print("Stopping the transactions")
        break
    else:
        atm_choice = input("Which ATM you want to transact in? 1 or 2 ")
        if atm_choice in ['1', '2']:
            producer.send("bankbranch", {'atmid': int(atm_choice), 'transid': transid})
            producer.flush()
            transid += 1
        else:
            print('Invalid ATM number')
            continue

producer.close()
```

In this code, the producer takes user input to continuously send transaction messages to the `bankbranch` topic. You can observe the consumer getting the events streamed by the producer in real time.

--- 

Feel free to adjust any parts of the markdown as needed!
