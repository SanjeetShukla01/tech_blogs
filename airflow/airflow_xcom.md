## Xcom
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

In this example, the push_task pushes the value "Hello, XCom!" to XCom using xcom_push. The pull_task then pulls the 
value using xcom_pull and prints it. The provide_context=True parameter in the PythonOperator ensures that the task 
receives the context, including the TaskInstance (ti), which is necessary for working with XCom.


## XCOM and Jinja 
In Apache Airflow, you can use XComs (Cross-Communication) in Jinja templates to dynamically reference values produced
by other tasks within your DAG. This can be helpful when you want to pass information between tasks or make decisions
based on the output of a previous task. Here's an example of how you can use XComs in Jinja templates:

Let's assume you have a DAG with two tasks: task_a and task_b. task_a pushes a value to XCom, and task_b uses the value
in its Jinja template:

```python
from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.python_operator import PythonOperator, BranchPythonOperator

def push_function(**kwargs):
    value_to_push = "Hello, XCom!"
    kwargs['ti'].xcom_push(key='example_key', value=value_to_push)

def pull_function(**kwargs):
    ti = kwargs['ti']
    pulled_value = ti.xcom_pull(task_ids='push_task', key='example_key')
    return pulled_value

def decide_branch(**kwargs):
    ti = kwargs['ti']
    pulled_value = ti.xcom_pull(task_ids='push_task', key='example_key')
    if pulled_value == "Hello, XCom!":
        return 'continue_task'
    else:
        return 'stop_task'

def print_value(**kwargs):
    ti = kwargs['ti']
    pulled_value = ti.xcom_pull(task_ids='push_task', key='example_key')
    print(f"Received value in task_b: {pulled_value}")

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2024, 1, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

dag = DAG(
    'example_dag',
    default_args=default_args,
    schedule_interval=timedelta(days=1),
)

push_task = PythonOperator(
    task_id='push_task',
    python_callable=push_function,
    provide_context=True,
    dag=dag,
)

branch_task = BranchPythonOperator(
    task_id='branch_task',
    python_callable=decide_branch,
    provide_context=True,
    dag=dag,
)

continue_task = PythonOperator(
    task_id='continue_task',
    python_callable=print_value,
    provide_context=True,
    dag=dag,
)

stop_task = PythonOperator(
    task_id='stop_task',
    python_callable=lambda **kwargs: print("Stopping task_b."),
    dag=dag,
)

push_task >> branch_task
branch_task >> [continue_task, stop_task]

```

In this example, task_a (push_task) pushes the value "Hello, XCom!" to XCom. task_b (branch_task) then uses a 
BranchPythonOperator to decide whether to continue (continue_task) or stop (stop_task) based on the value retrieved from XCom. The continue_task uses the value in its Jinja template.

The Jinja template in the continue_task might look like this:

```python
# Inside the PythonOperator task 'continue_task'
templated_command = """
{% set value_from_xcom = task_instance.xcom_pull(task_ids='push_task', key='example_key') %}
echo "Received value in task_b: {{ value_from_xcom }}"
"""

continue_task = PythonOperator(
    task_id='continue_task',
    python_callable=None,  # Set to None when using templates
    provide_context=True,
    templates_dict={'templated_command': templated_command},  # Pass the template to the task
    dag=dag,
)

```

Here, {{ value_from_xcom }} is replaced by the value retrieved from XCom during the template rendering process.
This way, you can dynamically reference XCom values within Jinja templates in your Airflow DAG.