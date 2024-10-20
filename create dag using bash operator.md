Here’s the content reformatted into well-structured Markdown:

# Exercise 3: Create a DAG

Let's create a DAG that runs daily, extracts user information from the `/etc/passwd` file, transforms it, and loads it into a file.

This DAG will have two tasks:
- **extract**: Extracts fields from the `/etc/passwd` file.
- **transform_and_load**: Transforms and loads data into a file.

### DAG Code

```python
# Import the libraries
from datetime import timedelta
# The DAG object; we'll need this to instantiate a DAG
from airflow.models import DAG
# Operators; you need this to write tasks!
from airflow.operators.bash_operator import BashOperator
# This makes scheduling easy
from airflow.utils.dates import days_ago

# Defining DAG arguments
default_args = {
    'owner': 'your_name_here',
    'start_date': days_ago(0),
    'email': ['your_email_here'],
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Defining the DAG
dag = DAG(
    'my-first-dag',
    default_args=default_args,
    description='My first DAG',
    schedule_interval=timedelta(days=1),
)

# Define the tasks
# Define the first task
extract = BashOperator(
    task_id='extract',
    bash_command='cut -d":" -f1,3,6 /etc/passwd > /home/project/airflow/dags/extracted-data.txt',
    dag=dag,
)

# Define the second task
transform_and_load = BashOperator(
    task_id='transform',
    bash_command='tr ":" "," < /home/project/airflow/dags/extracted-data.txt > /home/project/airflow/dags/transformed-data.csv',
    dag=dag,
)

# Task pipeline
extract >> transform_and_load
```

### Instructions
1. Create a new file named `my_first_dag.py`.
2. Copy the code above and paste it into `my_first_dag.py`.

---

# Exercise 4: Submit a DAG

Submitting a DAG is as simple as copying the DAG Python file into the `dags` folder in the `AIRFLOW_HOME` directory.

Airflow searches for Python source files within the specified `DAGS_FOLDER`, which can be found in the `airflow.cfg` file. The default path is `/home/project/airflow/dags`.

## Submission Steps

1. **Set the AIRFLOW_HOME**:
   Open a terminal and run:
   ```bash
   export AIRFLOW_HOME=/home/project/airflow
   echo $AIRFLOW_HOME
   ```

2. **Submit the DAG**:
   Run the command to copy the DAG:
   ```bash
   cp my_first_dag.py $AIRFLOW_HOME/dags
   ```

3. **Verify Submission**:
   List all existing DAGs:
   ```bash
   airflow dags list
   ```

   Verify that `my-first-dag` is part of the output:
   ```bash
   airflow dags list | grep "my-first-dag"
   ```

4. **List Tasks in the DAG**:
   Run the following command to see the tasks in `my-first-dag`:
   ```bash
   airflow tasks list my-first-dag
   ```

---

# Practice Exercise: Create ETL DAG

Write a DAG named `ETL_Server_Access_Log_Processing.py`.

## Steps

1. **Create the Imports Block**.
2. **Create the DAG Arguments Block** (use default settings).
3. **Create the DAG Definition Block** (the DAG should run daily).
4. **Create the Download Task**:
   - Download the server access log file from:
     ```
     https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Build%20a%20DAG%20using%20Airflow/web-server-access-log.txt
     ```

5. **Create the Extract Task**:
   - The log file contains the following fields:
     - **timestamp** - TIMESTAMP
     - **latitude** - float
     - **longitude** - float
     - **visitorid** - char(37)
     - **accessed_from_mobile** - boolean
     - **browser_code** - int
   - Extract the **timestamp** and **visitorid** fields.

6. **Create the Transform Task**:
   - Capitalize the **visitorid**.

7. **Create the Load Task**:
   - Compress the extracted and transformed data.

8. **Create the Task Pipeline**:
   - Schedule the tasks in this order:
     - `download`
     - `extract`
     - `transform`
     - `load`

9. **Submit the DAG**.

10. **Verify the Submission**.

## Code Example

Add the following code to `ETL_Server_Access_Log_Processing.py`:

```python
# Import the libraries
from datetime import timedelta
from airflow.models import DAG
from airflow.operators.bash_operator import BashOperator
from airflow.utils.dates import days_ago

# Defining DAG arguments
default_args = {
    'owner': 'your_name',
    'start_date': days_ago(0),
    'email': ['your_email'],
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Define the DAG
dag = DAG(
    'ETL_Server_Access_Log_Processing',
    default_args=default_args,
    description='DAG for processing server access logs',
    schedule_interval=timedelta(days=1),
)

# Define the download task
download = BashOperator(
    task_id='download',
    bash_command='curl "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Build%20a%20DAG%20using%20Airflow/web-server-access-log.txt" -o web-server-access-log.txt',
    dag=dag,
)

# Define the extract task
extract = BashOperator(
    task_id='extract',
    bash_command='cut -f1,4 -d"#" web-server-access-log.txt > /home/project/airflow/dags/extracted.txt',
    dag=dag,
)

# Define the transform task
transform = BashOperator(
    task_id='transform',
    bash_command='tr "[a-z]" "[A-Z]" < /home/project/airflow/dags/extracted.txt > /home/project/airflow/dags/capitalized.txt',
    dag=dag,
)

# Define the load task
load = BashOperator(
    task_id='load',
    bash_command='zip /home/project/airflow/dags/log.zip /home/project/airflow/dags/capitalized.txt',
    dag=dag,
)

# Task pipeline
download >> extract >> transform >> load
```

### Submission Command

Submit the DAG by running:
```bash
cp ETL_Server_Access_Log_Processing.py $AIRFLOW_HOME/dags
```

### Verification Command

Verify if the DAG is submitted:
```bash
airflow dags list
```

---

This format makes it easier to read and follow along with the instructions! If you need further adjustments, let me know!
