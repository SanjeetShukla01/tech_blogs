In Apache Airflow, sensors are a type of operator that will keep running until a certain criterion is met. 
They are typically used to wait for external conditions or events before allowing the workflow to proceed. 
Here are some commonly used Airflow sensors:

- **BaseSensorOperator:**
This is the base class for all sensors in Airflow. It provides the common functionalities and attributes for sensors.

- **ExternalTaskSensor:**
Waits for a different DAG's task to complete before allowing the workflow to proceed.

- **FileSensor:**
Monitors a file or a directory, waiting for it to be created or modified.

- **HttpSensor:**
Pings a HTTP endpoint until a specified response is received.

- **SqlSensor:**
Executes a SQL query and waits for a specific condition defined in the query.

- **S3KeySensor:**
Waits for a key (object) to appear in an Amazon S3 bucket.

- **TimeDeltaSensor:**
Waits for a specific time duration before proceeding.

- **TimeSensor:**
Waits until a specific time of the day is reached.

- **NamedHivePartitionSensor:**
Waits for a Hive partition to be created.

- **HdfsSensor:**
Waits for a file or folder to exist on HDFS (Hadoop Distributed File System).

- **DagRunSensor:**
Waits for a specific DagRun to be completed. 


These sensors provide a flexible way to design workflows that depend on external events or conditions. 
You can choose the appropriate sensor based on the nature of the condition you are waiting for in your workflow.


Code Examples:

**External Task Sensor:**

```python
from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.sensors.external_task_sensor import ExternalTaskSensor

# Define the default_args dictionary to provide default parameters to the DAG
default_args = {
    'owner': 'airflow',
    'start_date': datetime(2022, 1, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Instantiate a DAG
dag = DAG(
    'example_external_task_sensor',
    default_args=default_args,
    schedule_interval='@daily',  # Set the schedule interval as needed
)

# Define tasks
start_task = DummyOperator(task_id='start_task', dag=dag)

# ExternalTaskSensor waits for the completion of a task in another DAG
external_sensor_task = ExternalTaskSensor(
    task_id='external_sensor_task',
    external_dag_id='other_dag_id',  # Specify the ID of the other DAG
    external_task_id='task_to_wait_for',  # Specify the task ID to wait for
    timeout=3600,  # Specify the maximum amount of time to wait (in seconds)
    mode='poke',  # Use 'poke' mode to actively check for the external task completion
    poke_interval=60,  # Set the interval for checking (in seconds)
    retries=3,  # Set the number of retries
    dag=dag,
)

end_task = DummyOperator(task_id='end_task', dag=dag)

# Define the task sequence
start_task >> external_sensor_task >> end_task
```

**File Sensor**
```python

from datetime import datetime, timedelta
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.sensors.filesystem import FileSensor

# Define the default_args dictionary
default_args = {
    'owner': 'airflow',
    'start_date': datetime(2022, 1, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# Instantiate a DAG
dag = DAG(
    'example_file_sensor',
    default_args=default_args,
    schedule_interval='@daily',
)

# Define tasks
start_task = DummyOperator(task_id='start_task', dag=dag)

# FileSensor waits for a file or directory to appear or be modified
file_sensor_task = FileSensor(
    task_id='file_sensor_task',
    filepath='/path/to/your/file.txt',  # Specify the path to the file to monitor
    poke_interval=60,  # Set the interval for checking (in seconds)
    retries=3,  # Set the number of retries
    mode='poke',  # Use 'poke' mode for actively checking
    dag=dag,
)

end_task = DummyOperator(task_id='end_task', dag=dag)

# Define the task sequence
start_task >> file_sensor_task >> end_task
```

