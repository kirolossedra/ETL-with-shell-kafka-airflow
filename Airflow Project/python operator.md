Here's the formatted content for your Airflow DAG setup, organized in a clean Markdown format for clarity.

---

# Airflow ETL DAG Setup

## Exercise 1: Prepare the Lab Environment

1. **Start Apache Airflow.**
2. **Open Apache Airflow in IDE.**
3. **Wait until Airflow starts up fully and is active before proceeding.**
   - If there’s an error, restart Airflow.

4. **Create the Directory Structure for the Staging Area:**
   ```bash
   sudo mkdir -p /home/project/airflow/dags/python_etl/staging
   ```

5. **Avoid Permission Issues:**
   ```bash
   sudo chmod -R 777 /home/project/airflow/dags/python_etl
   ```

---

## Exercise 2: Add Imports, Define DAG Arguments, and Define DAG

1. **Create a file named `ETL_toll_data.py` in the `/home/project` directory.**

2. **Add the necessary imports and DAG arguments:**
   ```python
   from datetime import timedelta
   from airflow.models import DAG
   from airflow.operators.python import PythonOperator
   from airflow.utils.dates import days_ago
   import requests
   import tarfile
   import csv
   import shutil

   # Define the path for the input and output files
   source_url = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Final%20Assignment/tolldata.tgz'
   destination_path = '/home/project/airflow/dags/python_etl/staging'

   # Default arguments for the DAG
   default_args = {
       'owner': 'Your name',  # Replace with your name
       'start_date': days_ago(0),
       'email': ['your email'],  # Replace with your email
       'retries': 1,
       'retry_delay': timedelta(minutes=5),
   }

   # Define the DAG
   dag = DAG(
       'ETL_toll_data',
       default_args=default_args,
       description='Apache Airflow Final Assignment',
       schedule_interval=timedelta(days=1),
   )
   ```

---

## Exercise 3: Create Python Functions

1. **Define the functions for the ETL process:**

   ```python
   # Function to download the dataset
   def download_dataset():
       response = requests.get(source_url, stream=True)
       if response.status_code == 200:
           with open(f"{destination_path}/tolldata.tgz", 'wb') as f:
               f.write(response.raw.read())
       else:
           print("Failed to download the file")

   # Function to untar the dataset
   def untar_dataset():
       with tarfile.open(f"{destination_path}/tolldata.tgz", "r:gz") as tar:
           tar.extractall(path=destination_path)

   # Function to extract data from CSV
   def extract_data_from_csv():
       input_file = f"{destination_path}/vehicle-data.csv"
       output_file = f"{destination_path}/csv_data.csv"
       with open(input_file, 'r') as infile, open(output_file, 'w') as outfile:
           writer = csv.writer(outfile)
           writer.writerow(['Rowid', 'Timestamp', 'Anonymized Vehicle number', 'Vehicle type'])
           for line in infile:
               row = line.split(',')
               writer.writerow([row[0], row[1], row[2], row[3]])

   # Function to extract data from TSV
   def extract_data_from_tsv():
       input_file = f"{destination_path}/tollplaza-data.tsv"
       output_file = f"{destination_path}/tsv_data.csv"
       with open(input_file, 'r') as infile, open(output_file, 'w') as outfile:
           writer = csv.writer(outfile)
           writer.writerow(['Number of axles', 'Tollplaza id', 'Tollplaza code'])
           for line in infile:
               row = line.split('\t')
               writer.writerow([row[0], row[1], row[2]])

   # Function to extract data from fixed-width file
   def extract_data_from_fixed_width():
       input_file = f"{destination_path}/payment-data.txt"
       output_file = f"{destination_path}/fixed_width_data.csv"
       with open(input_file, 'r') as infile, open(output_file, 'w') as outfile:
           writer = csv.writer(outfile)
           writer.writerow(['Type of Payment code', 'Vehicle Code'])
           for line in infile:
               writer.writerow([line[0:6].strip(), line[6:12].strip()])

   # Function to consolidate data
   def consolidate_data():
       csv_file = f"{destination_path}/csv_data.csv"
       tsv_file = f"{destination_path}/tsv_data.csv"
       fixed_width_file = f"{destination_path}/fixed_width_data.csv"
       output_file = f"{destination_path}/extracted_data.csv"

       with open(csv_file, 'r') as csv_in, open(tsv_file, 'r') as tsv_in, open(fixed_width_file, 'r') as fixed_in, open(output_file, 'w') as out_file:
           csv_reader = csv.reader(csv_in)
           tsv_reader = csv.reader(tsv_in)
           fixed_reader = csv.reader(fixed_in)
           writer = csv.writer(out_file)

           writer.writerow(['Rowid', 'Timestamp', 'Anonymized Vehicle number', 'Vehicle type', 'Number of axles', 'Tollplaza id', 'Tollplaza code', 'Type of Payment code', 'Vehicle Code'])
           next(csv_reader)
           next(tsv_reader)
           next(fixed_reader)

           for csv_row, tsv_row, fixed_row in zip(csv_reader, tsv_reader, fixed_reader):
               writer.writerow(csv_row + tsv_row + fixed_row)

   # Function to transform data
   def transform_data():
       input_file = f"{destination_path}/extracted_data.csv"
       output_file = f"{destination_path}/transformed_data.csv"

       with open(input_file, 'r') as infile, open(output_file, 'w') as outfile:
           reader = csv.DictReader(infile)
           writer = csv.DictWriter(outfile, fieldnames=reader.fieldnames)
           writer.writeheader()

           for row in reader:
               row['Vehicle type'] = row['Vehicle type'].upper()
               writer.writerow(row)
   ```

---

## Exercise 4: Create Tasks Using Python Operators and Define Pipeline

1. **Define the tasks using `PythonOperator`:**

   ```python
   # Define the tasks
   download_task = PythonOperator(
       task_id='download_dataset',
       python_callable=download_dataset,
       dag=dag,
   )

   untar_task = PythonOperator(
       task_id='untar_dataset',
       python_callable=untar_dataset,
       dag=dag,
   )

   extract_csv_task = PythonOperator(
       task_id='extract_data_from_csv',
       python_callable=extract_data_from_csv,
       dag=dag,
   )

   extract_tsv_task = PythonOperator(
       task_id='extract_data_from_tsv',
       python_callable=extract_data_from_tsv,
       dag=dag,
   )

   extract_fixed_width_task = PythonOperator(
       task_id='extract_data_from_fixed_width',
       python_callable=extract_data_from_fixed_width,
       dag=dag,
   )

   consolidate_task = PythonOperator(
       task_id='consolidate_data',
       python_callable=consolidate_data,
       dag=dag,
   )

   transform_task = PythonOperator(
       task_id='transform_data',
       python_callable=transform_data,
       dag=dag,
   )

   # Set the task dependencies
   download_task >> untar_task >> [extract_csv_task, extract_tsv_task, extract_fixed_width_task] >> consolidate_task >> transform_task
   ```

---

## Exercise 5: Save, Submit, and Run DAG

1. **Save the DAG you defined.**
2. **Submit the DAG by copying it into the `$AIRFLOW_HOME/dags` directory.**
3. **Use CLI or Web UI to unpause the task.**
4. **Observe the outcome of the tasks in the Airflow console.**

---

Feel free to reach out if you have any questions or need further assistance!
