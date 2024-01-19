XCom (Cross-Communication) is a feature in Apache Airflow, which is an open-source platform for orchestrating complex 
workflows and data processing pipelines. XCom allows tasks to exchange small amounts of data between them.

In Airflow, tasks within a workflow can share information by using the xcom_push and xcom_pull methods. The xcom_push
method allows a task to push a value to XCom, and the xcom_pull method allows another task to retrieve that value.

Here's a simple example of how you might use XCom in an Airflow DAG (Directed Acyclic Graph):

```python
from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.python_operator import PythonOperator

# Define a function that will be used as a task
def push_function(**kwargs):
    value_to_push = "Hello, XCom!"
    kwargs['ti'].xcom_push(key='example_key', value=value_to_push)

def pull_function(**kwargs):
    # Retrieve the value pushed by the previous task
    ti = kwargs['ti']
    pulled_value = ti.xcom_pull(task_ids='push_task', key='example_key')
    print(f"Received value: {pulled_value}")

# Define the default_args dictionary to specify the DAG's default parameters
default_args = {
    'owner': 'airflow',
    'start_date': datetime(2024, 1, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Instantiate the DAG
dag = DAG(
    'example_dag',
    default_args=default_args,
    schedule_interval=timedelta(days=1),  # Set the schedule interval for the DAG
)

# Define two tasks, one to push a value and another to pull and print it
push_task = PythonOperator(
    task_id='push_task',
    python_callable=push_function,
    provide_context=True,  # This ensures the task receives the context (including the TaskInstance)
    dag=dag,
)

pull_task = PythonOperator(
    task_id='pull_task',
    python_callable=pull_function,
    provide_context=True,
    dag=dag,
)

# Set the task dependencies
push_task >> pull_task

```