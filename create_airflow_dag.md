# Exercise 3: Create a DAG with PythonOperator

In this exercise, you will create a Directed Acyclic Graph (DAG) that defines a pipeline for extract, transform, load, and check operations using `PythonOperator`. Follow the steps below to create your DAG.

## Step 1: Create the DAG File

Create a new file named `my_first_dag.py`. This file will define the tasks: `execute_extract`, `execute_transform`, `execute_load`, and `execute_check`.

### Sample Code for `my_first_dag.py`

```python
# Import the libraries
from datetime import timedelta
from airflow.models import DAG
from airflow.operators.python import PythonOperator
from airflow.utils.dates import days_ago

# Define the path for the input and output files
input_file = '/etc/passwd'
extracted_file = 'extracted-data.txt'
transformed_file = 'transformed.txt'
output_file = 'data_for_analytics.csv'

def extract():
    print("Inside Extract")
    with open(input_file, 'r') as infile, open(extracted_file, 'w') as outfile:
        for line in infile:
            fields = line.split(':')
            if len(fields) >= 6:
                outfile.write(f"{fields[0]}:{fields[2]}:{fields[5]}\n")

def transform():
    print("Inside Transform")
    with open(extracted_file, 'r') as infile, open(transformed_file, 'w') as outfile:
        for line in infile:
            outfile.write(line.replace(':', ',') + '\n')

def load():
    print("Inside Load")
    with open(transformed_file, 'r') as infile, open(output_file, 'w') as outfile:
        for line in infile:
            outfile.write(line)

def check():
    print("Inside Check")
    with open(output_file, 'r') as infile:
        for line in infile:
            print(line)

# Default arguments for the DAG
default_args = {
    'owner': 'Your name',
    'start_date': days_ago(0),
    'email': ['your email'],
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Define the DAG
dag = DAG(
    'my-first-python-etl-dag',
    default_args=default_args,
    description='My first DAG',
    schedule_interval=timedelta(days=1),
)

# Define the tasks
execute_extract = PythonOperator(task_id='extract', python_callable=extract, dag=dag)
execute_transform = PythonOperator(task_id='transform', python_callable=transform, dag=dag)
execute_load = PythonOperator(task_id='load', python_callable=load, dag=dag)
execute_check = PythonOperator(task_id='check', python_callable=check, dag=dag)

# Task pipeline
execute_extract >> execute_transform >> execute_load >> execute_check
```

## Step 2: Submit the DAG

To submit the DAG, copy the file into the `dags` directory of your Airflow installation.

```bash
export AIRFLOW_HOME=/home/project/airflow
echo $AIRFLOW_HOME
cp my_first_dag.py $AIRFLOW_HOME/dags
```

## Step 3: Verify the DAG Submission

To check if your DAG has been successfully submitted, list all existing DAGs:

```bash
airflow dags list
```

To verify that `my-first-python-etl-dag` is part of the output:

```bash
airflow dags list | grep "my-first-python-etl-dag"
```

## Step 4: List Tasks in Your DAG

To list all the tasks in your newly created DAG, run:

```bash
airflow tasks list my-first-python-etl-dag
```

## Exercise 4: Create the ETL DAG for Server Access Logs

### Step 1: Create the DAG File

Create a new file named `ETL_Server_Access_Log_Processing.py`.

### Sample Code for `ETL_Server_Access_Log_Processing.py`

```python
# Import the libraries
from datetime import timedelta
from airflow.models import DAG
from airflow.operators.python import PythonOperator
import requests

# Define file paths
input_file = 'web-server-access-log.txt'
extracted_file = 'extracted-data.txt'
transformed_file = 'transformed.txt'
output_file = 'capitalized.txt'

def download_file():
    url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Build%20a%20DAG%20using%20Airflow/web-server-access-log.txt"
    response = requests.get(url)
    with open(input_file, 'wb') as file:
        file.write(response.content)
    print(f"File downloaded successfully: {input_file}")

def extract():
    print("Inside Extract")
    with open(input_file, 'r') as infile, open(extracted_file, 'w') as outfile:
        for line in infile:
            fields = line.split('#')
            if len(fields) >= 4:
                outfile.write(f"{fields[0]}#{fields[3]}\n")

def transform():
    print("Inside Transform")
    with open(extracted_file, 'r') as infile, open(transformed_file, 'w') as outfile:
        for line in infile:
            outfile.write(line.upper())

def load():
    print("Inside Load")
    with open(transformed_file, 'r') as infile, open(output_file, 'w') as outfile:
        for line in infile:
            outfile.write(line)

def check():
    print("Inside Check")
    with open(output_file, 'r') as infile:
        for line in infile:
            print(line)

# Default arguments for the DAG
default_args = {
    'owner': 'Your name',
    'start_date': days_ago(0),
    'email': ['your email'],
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Define the DAG
dag = DAG(
    'etl-server-access-log-processing',
    default_args=default_args,
    description='ETL for server access logs',
    schedule_interval=timedelta(days=1),
)

# Define the tasks
download = PythonOperator(task_id='download', python_callable=download_file, dag=dag)
execute_extract = PythonOperator(task_id='extract', python_callable=extract, dag=dag)
execute_transform = PythonOperator(task_id='transform', python_callable=transform, dag=dag)
execute_load = PythonOperator(task_id='load', python_callable=load, dag=dag)
execute_check = PythonOperator(task_id='check', python_callable=check, dag=dag)

# Task pipeline
download >> execute_extract >> execute_transform >> execute_load >> execute_check
```

### Step 2: Submit the DAG

Copy the new file into the `dags` directory:

```bash
cp ETL_Server_Access_Log_Processing.py $AIRFLOW_HOME/dags
```

### Step 3: Verify the DAG Submission

Check if the DAG is submitted:

```bash
airflow dags list | grep etl-server-access-log-processing
```

If the DAG doesn't appear, check for errors with:

```bash
airflow dags list-import-errors
```

Now you've created and submitted your ETL DAG!
