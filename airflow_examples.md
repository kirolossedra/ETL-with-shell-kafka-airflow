# Exercises on Apache Airflow CLI

## Exercise 3: Apache Airflow CLI Commands

Apache Airflow provides various command line options for managing Directed Acyclic Graphs (DAGs).

### List Existing DAGs

To list all the existing DAGs, run the following command in the terminal:
```bash
airflow dags list
```

### List Tasks in a Specific DAG

To list all tasks in the DAG named `example_bash_operator`, run:
```bash
airflow tasks list example_bash_operator
```

### Practice Exercise

To list all tasks for the DAG named `tutorial`, run:
```bash
airflow tasks list tutorial
```

---

## Exercise 4: Pause or Unpause a DAG

### Unpause a DAG

To unpause a DAG named `tutorial`, run:
```bash
airflow dags unpause tutorial
```

### Pause a DAG

To pause the DAG named `tutorial`, run:
```bash
airflow dags pause tutorial
```

### Practice Exercise

To unpause the DAG named `example_branch_operator`, run:
```bash
airflow dags unpause example_branch_operator
```

---

## Additional Practice Exercises

### List Tasks for `example_branch_labels`

To list tasks for the DAG `example_branch_labels`, run:
```bash
airflow tasks list example_branch_labels
```

### Unpause `example_branch_labels`

To unpause the DAG `example_branch_labels`, run:
```bash
airflow dags unpause example_branch_labels
```

### Pause `example_branch_labels`

To pause the DAG `example_branch_labels`, run:
```bash
airflow dags pause example_branch_labels
``` 

Feel free to practice these commands to familiarize yourself with Apache Airflow CLI!
