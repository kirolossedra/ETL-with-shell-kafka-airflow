Here's the formatted markdown for your exercises:

---

# Exercise 3: Create a Topic and Producer for Processing Bank ATM Transactions

Next, you will create a `bankbranch` topic to process messages from bank branch ATM machines.

Suppose the messages come from the ATM in the form of a simple JSON object, including an ATM ID and a transaction ID like the following example:

```json
{"atmid": 1, "transid": 100}
```

To process the ATM messages, you will first need to create a new topic called `bankbranch`.

## Step 1: Create the Topic

Open a new terminal and change to the `kafka_2.13-3.8.0` directory:

```bash
cd kafka_2.13-3.8.0
```

Create a new topic using the `--topic` argument with the name `bankbranch`. Specify the `--partitions 2` argument to create two partitions for this topic:

```bash
bin/kafka-topics.sh --create --topic bankbranch --partitions 2 --bootstrap-server localhost:9092
```

List all topics to check if `bankbranch` has been created successfully:

```bash
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

You can also use the `--describe` command to check the details of the topic `bankbranch`:

```bash
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic bankbranch
```

You should see the `bankbranch` topic listed with two partitions. If no message keys are specified, messages will be published to these two partitions in an alternating sequence like this:

- Partition 0 -> Partition 1 -> Partition 0 -> Partition 1 …

## Step 2: Create a Producer

Next, create a producer to publish ATM transaction messages. Run the following command to create a producer for the topic `bankbranch`:

```bash
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic bankbranch
```

To produce the messages, look for the `>` prompt and add the following ATM messages:

```json
{"atmid": 1, "transid": 100}
{"atmid": 1, "transid": 101}
{"atmid": 2, "transid": 200}
{"atmid": 1, "transid": 102}
{"atmid": 2, "transid": 201}
```

## Step 3: Create a Consumer

Now, create a consumer in a new terminal window to consume these five new messages. Open a new terminal and change to the `kafka_2.13-3.8.0` directory:

```bash
cd kafka_2.13-3.8.0
```

Then start a new consumer to subscribe to the `bankbranch` topic:

```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch --from-beginning
```

You should be able to view the five new messages that you published. However, the messages may not be consumed in the same order as they were published. Typically, you will need to keep the consumed messages sorted in their original published order, especially for critical use cases, such as financial transactions.

---

# Exercise 4: Produce and Consume with Message Keys

In this step, you will use message keys to ensure that messages with the same key are consumed in the same order as they were published. 

## Step 1: Set Up Terminals

At this point, you should have the following three terminals open:

1. Kafka Server terminal
2. Producer terminal
3. Consumer terminal

### Stop Previous Consumers

First, go to the consumer terminal and stop the consumer using `Ctrl + C` (Windows) or `COMMAND + .` (Mac).

Then, switch to the Producer terminal and stop the previous producer.

## Step 2: Start a New Producer

Start a new producer with the following message key options:

- `--property parse.key=true` to make the producer parse message keys
- `--property key.separator=:` to define the key separator

Start a new producer with the message key enabled:

```bash
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic bankbranch --property parse.key=true --property key.separator=:
```

Once you see the `>` symbol, produce the following messages, where you define each key to match the ATM ID for each message:

```json
1:{"atmid": 1, "transid": 103}
1:{"atmid": 1, "transid": 104}
2:{"atmid": 2, "transid": 202}
2:{"atmid": 2, "transid": 203}
1:{"atmid": 1, "transid": 105}
```

## Step 3: Start a New Consumer

Switch to the consumer terminal again and start a new consumer with `--property print.key=true` and `--property key.separator=:` arguments to print the keys:

```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch --from-beginning --property print.key=true --property key.separator=:
```

You should now see that messages with the same key are being consumed in the same order (for example: trans102 -> trans103 -> trans104) as they were published.

### Message Distribution in Partitions

With two partitions and no message keys specified, transaction messages were published to the two partitions in rotation:

- **Partition 0:** `[{"atmid": 1, "transid": 100}, {"atmid": 2, "transid": 200}, {"atmid": 2, "transid": 201}]`
- **Partition 1:** `[{"atmid": 1, "transid": 101}, {"atmid": 1, "transid": 102}]`

With message keys specified, the messages will look like this:

- **Partition 0:** `[{"atmid": 1, "transid": 103}, {"atmid": 1, "transid": 104}, {"atmid": 1, "transid": 105}]`
- **Partition 1:** `[{"atmid": 2, "transid": 202}, {"atmid": 2, "transid": 203}]`

Messages with the same key will always be published to the same partition, preserving their published order.

---

# Exercise 5: Consumer Offset

Topic partitions keep published messages in a sequence. The message offset indicates a message's position in that sequence. 

## Step 1: Create a Consumer Group

In the consumer terminal, stop the previous consumer if it is still running. Run the following command to create a new consumer within a consumer group called `atm-app`:

```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch --group atm-app
```

After the consumer within the `atm-app` consumer group starts, you should not expect any messages to be consumed because previous consumers have already consumed all messages.

### Verify Consumer Group Details

You can verify this by checking the consumer group details:

```bash
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group atm-app
```

You should see offset information for the topic `bankbranch`. 

### Produce More Messages

Next, produce two more messages:

```json
1:{"atmid": 1, "transid": 106}
2:{"atmid": 2, "transid": 204}
```

Switch back to the consumer terminal and check the consumer group details again:

```bash
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group atm-app
```

You should see that offsets have increased by 1, indicating new messages are available for consumption.

## Step 2: Reset Offset

To reset the index to consume messages again from the beginning, stop the previous consumer if it is still running and run the following command:

```bash
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --topic bankbranch --group atm-app --reset-offsets --to-earliest --execute
```

Now, start the consumer again:

```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch --group atm-app
```

You should see that all messages are consumed.

### Resetting to a Specific Position

To consume only the last two messages, reset the offset using:

```bash
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --topic bankbranch --group atm-app --reset-offsets --shift-by -2 --execute
```

When you run the consumer again, you should see that you've consumed four messages (two for each partition).

Finally, stop your producer, consumer, and the Kafka server.

--- 

Feel free to modify any part of the markdown as needed!
