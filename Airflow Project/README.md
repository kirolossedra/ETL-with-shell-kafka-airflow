
Here's the structured markdown for your lab exercises:

---

# Exercise 1: Set Up the Lab Environment

## Step 1: Start Apache Airflow

Open Apache Airflow in your IDE.

## Step 2: Create Directory Structure

Open a terminal and create the staging area:

```bash
sudo mkdir -p /home/project/airflow/dags/finalassignment/staging
```

## Step 3: Set Permissions

Execute the following command to give appropriate permissions:

```bash
sudo chmod -R 777 /home/project/airflow/dags/finalassignment
```

## Step 4: Download Dataset

Download the dataset using the curl command:

```bash
sudo curl https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Final%20Assignment/tolldata.tgz -o /home/project/airflow/dags/finalassignment/tolldata.tgz
```

---

# Exercise 2: Create Imports, DAG Arguments, and Definition

## Step 1: Create `ETL_toll_data.py`

Create a new file named `ETL_toll_data.py` in the `/home/project` directory.

## Step 2: Import Required Packages

Open `ETL_toll_data.py` in your file editor and import the necessary packages to build the DAG.

## Step 3: Define DAG Arguments

Define the following DAG arguments in `ETL_toll_data.py`:

| Parameter           | Value                 |
|---------------------|-----------------------|
| owner               | <Any dummy name>     |
| start_date          | today                 |
| email               | <Any dummy email>     |
| email_on_failure    | True                  |
| email_on_retry      | True                  |
| retries             | 1                     |
| retry_delay         | 5 minutes             |

*Take a screenshot of the task code and name it `dag_args.jpg`.*

## Step 4: Define the DAG

Define the DAG in `ETL_toll_data.py` with the following parameters:

| Parameter           | Value                      |
|---------------------|----------------------------|
| DAG id              | ETL_toll_data              |
| Schedule            | Daily once                 |
| default_args        | As defined above           |
| description         | Apache Airflow Final Assignment |

*Take a screenshot of the command and output used, naming it `dag_definition.jpg`.*

---

# Exercise 3: Create the Tasks Using BashOperator

## Step 1: Create `unzip_data` Task

Create a task named `unzip_data` to unzip the data. Use the following command:

```bash
tar -xvzf /home/project/airflow/dags/finalassignment/tolldata.tgz -C /home/project/airflow/dags/finalassignment/staging
```

*Take a screenshot of the task code and name it `unzip_data.jpg`.*

## Step 2: Create `extract_data_from_csv` Task

Create a task named `extract_data_from_csv` to extract specific fields from `vehicle-data.csv`:

```bash
cut -d, -f1,2,3,4 /home/project/airflow/dags/finalassignment/staging/vehicle-data.csv > /home/project/airflow/dags/finalassignment/staging/csv_data.csv
```

*Take a screenshot of the task code and name it `extract_data_from_csv.jpg`.*

## Step 3: Create `extract_data_from_tsv` Task

Create a task named `extract_data_from_tsv` to extract specific fields from `tollplaza-data.tsv`:

```bash
cut -f1,2,3 /home/project/airflow/dags/finalassignment/staging/tollplaza-data.tsv > /home/project/airflow/dags/finalassignment/staging/tsv_data.csv
```

*Take a screenshot of the task code and name it `extract_data_from_tsv.jpg`.*

## Step 4: Create `extract_data_from_fixed_width` Task

Create a task named `extract_data_from_fixed_width` to extract specific fields from `payment-data.txt`:

```bash
cut -c1-20,21-40 /home/project/airflow/dags/finalassignment/staging/payment-data.txt > /home/project/airflow/dags/finalassignment/staging/fixed_width_data.csv
```

*Take a screenshot of the task code and name it `extract_data_from_fixed_width.jpg`.*

## Step 5: Create `consolidate_data` Task

Create a task named `consolidate_data` to combine data from the extracted files into `extracted_data.csv`:

```bash
paste -d, /home/project/airflow/dags/finalassignment/staging/csv_data.csv /home/project/airflow/dags/finalassignment/staging/tsv_data.csv /home/project/airflow/dags/finalassignment/staging/fixed_width_data.csv > /home/project/airflow/dags/finalassignment/staging/extracted_data.csv
```

*Take a screenshot of the command and output used and name it `consolidate_data.jpg`.*

## Step 6: Create `transform_data` Task

Create a task named `transform_data` to transform the `vehicle_type` field in `extracted_data.csv` into uppercase and save it to `transformed_data.csv`:

```bash
tr '[:lower:]' '[:upper:]' < /home/project/airflow/dags/finalassignment/staging/extracted_data.csv > /home/project/airflow/dags/finalassignment/staging/transformed_data.csv
```

*Take a screenshot of the command and output used and name it `transform.jpg`.*

## Step 7: Define Task Pipeline

Define the task pipeline with the following order:

1. unzip_data
2. extract_data_from_csv
3. extract_data_from_tsv
4. extract_data_from_fixed_width
5. consolidate_data
6. transform_data

*Take a screenshot of the task pipeline section of the DAG and name it `task_pipeline.jpg`.*

---

# Exercise 4: Getting the DAG Operational

## Step 1: Submit the DAG

Use the CLI or Web UI to show that the DAG has been properly submitted. Take a screenshot showing the DAG in the list and name it `submit_dag.jpg`.

If the DAG is not found, check for errors using:

```bash
airflow dags list-import-errors
```

## Step 2: Unpause and Trigger the DAG

Unpause and trigger the DAG through CLI or Web UI. Take a screenshot of the DAG unpaused on CLI or the GUI and name it `unpause_trigger_dag.jpg`.

## Step 3: View DAG Tasks

Take a screenshot of the tasks in the DAG run through CLI or Web UI and name it `dag_tasks.jpg`.

## Step 4: View DAG Runs

Take a screenshot of the DAG runs for the Airflow console through CLI or Web UI and name it `dag_runs.jpg`.

---

# Screenshot Checklist

You should have the following screenshots with `.jpg` or `.png` extensions:

- `dag_args.jpg`
- `dag_definition.jpg`
- `unzip_data.jpg`
- `extract_data_from_csv.jpg`
- `extract_data_from_tsv.jpg`
- `extract_data_from_fixed_width.jpg`
- `consolidate_data.jpg`
- `transform.jpg`
- `task_pipeline.jpg`
- `submit_dag.jpg`
- `unpause_trigger_dag.jpg`
- `dag_tasks.jpg`
- `dag_runs.jpg`

This concludes the assignment.

--- 

Feel free to customize any part of this markdown to fit your needs!
