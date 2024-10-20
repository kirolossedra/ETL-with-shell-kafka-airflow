# Exercises on Data Manipulation in PostgreSQL

## Exercise 1 - Extracting Data Using `cut` Command

The `cut` command helps us extract selected characters or fields from a line of text.

### Extracting Characters

- **Extract the first four characters:**
    ```bash
    echo "database" | cut -c1-4
    ```
    Output: `data`

- **Extract the 5th to 8th characters:**
    ```bash
    echo "database" | cut -c5-8
    ```
    Output: `base`

- **Extract the 1st and 5th characters:**
    ```bash
    echo "database" | cut -c1,5
    ```
    Output: `db`

### Extracting Fields/Columns

You can extract a specific column from a delimited text file by specifying the delimiter with the `-d` option and the field number with the `-f` option. The `/etc/passwd` file is a `:` delimited file.

- **Extract usernames (the first field) from `/etc/passwd`:**
    ```bash
    cut -d":" -f1 /etc/passwd
    ```

- **Extract multiple fields (1st, 3rd, and 6th) from `/etc/passwd`:**
    ```bash
    cut -d":" -f1,3,6 /etc/passwd
    ```

- **Extract a range of fields (3rd to 6th) from `/etc/passwd`:**
    ```bash
    cut -d":" -f3-6 /etc/passwd
    ```

---

## Exercise 2 - Transforming Data Using `tr`

The `tr` command is used to translate, squeeze, and delete characters.

### Translate from One Character Set to Another

- **Translate lowercase to uppercase:**
    ```bash
    echo "Shell Scripting" | tr "[a-z]" "[A-Z]"
    ```

- **Using predefined character sets:**
    ```bash
    echo "Shell Scripting" | tr "[:lower:]" "[:upper:]"
    ```

- **Translate uppercase to lowercase:**
    ```bash
    echo "Shell Scripting" | tr "[A-Z]" "[a-z]"
    ```

### Squeeze Repeating Occurrences of Characters

- **Replace repeated spaces with a single space:**
    ```bash
    ps | tr -s " "
    ```

### Delete Characters

- **Delete all digits:**
    ```bash
    echo "My login pin is 5634" | tr -d "[:digit:]"
    ```
    Output: `My login pin is`

---

## Exercise 3 - Start the PostgreSQL Database

1. From the SkillsNetwork tools, select **PostgreSQL Database server** and click **Start**. This will take a few minutes.
2. Click on **PostgreSQL CLI** to start interacting with the server. You’ll see the `postgres=#` prompt.

---

## Exercise 4 - Create a Table

In this exercise, we will create a table called `users` in the PostgreSQL database.

### Table Structure

The `users` table will have the following columns:
- `uname`
- `uid`
- `home`

### Connect to the Database

To connect to the default `template1` database, run:
```bash
\c template1
```
You will see: `You are now connected to database "template1" as user "postgres".`

### Create the Table

Run the following statement:
```bash
create table users(username varchar(50), userid int, homedirectory varchar(100));
```
If successful, you’ll see: `CREATE TABLE`

---

## Exercise 5 - Loading Data into a PostgreSQL Table

### Create a Shell Script

Create a shell script named `csv2db.sh`:
```bash
touch csv2db.sh
```

### Script Content

Open `csv2db.sh` and add the following comments:
```bash
# This script
# Extracts data from /etc/passwd file into a CSV file.
# The csv data file contains the user name, user id, and
# home directory of each user account defined in /etc/passwd
# Transforms the text delimiter from ":" to ",".
# Loads the data from the CSV file into a table in PostgreSQL database.
```

### Extract User Data

Add the following lines to extract user information:
```bash
# Extract phase
echo "Extracting data"
cut -d":" -f1,3,6 /etc/passwd
```

### Redirect Output to File

Change the script to redirect the extracted data into `extracted-data.txt`:
```bash
cut -d":" -f1,3,6 /etc/passwd > extracted-data.txt
```

### Transform Data

To replace colons with commas and save as `transformed-data.csv`, add:
```bash
# Transform phase
echo "Transforming data"
tr ":" "," < extracted-data.txt > transformed-data.csv
```

### Load Data into PostgreSQL

Add the following to load the data:
```bash
# Load phase
echo "Loading data"
export PGPASSWORD=<yourpassword>;
echo "\c template1;\COPY users FROM '/home/project/transformed-data.csv' DELIMITERS ',' CSV;" | psql --username=postgres
```

---

## Exercise 6 - Execute the Final Script

### Run the Script

Execute the script:
```bash
bash csv2db.sh
```

### Verify Data in the Table

Add the following line to query the data:
```bash
echo "SELECT * FROM users;" | psql --username=postgres --host=postgres template1
```

### Final Execution

Run the script again to verify that the `users` table is populated with data:
```bash
bash csv2db.sh
```

Congratulations! You have created an ETL script using shell scripting.
