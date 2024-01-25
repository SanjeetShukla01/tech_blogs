In Apache Airflow, sensors are a type of operator that will keep running until a certain criterion is met. 
They are typically used to wait for external conditions or events before allowing the workflow to proceed. 
Here are some commonly used Airflow sensors:

**BaseSensorOperator:**

This is the base class for all sensors in Airflow. It provides the common functionalities and attributes for sensors.

**ExternalTaskSensor:**

Waits for a different DAG's task to complete before allowing the workflow to proceed.

**FileSensor:**

Monitors a file or a directory, waiting for it to be created or modified.

**HttpSensor:**

Pings a HTTP endpoint until a specified response is received.

**SqlSensor:**

Executes a SQL query and waits for a specific condition defined in the query.

**S3KeySensor:**

Waits for a key (object) to appear in an Amazon S3 bucket.

**TimeDeltaSensor:**

Waits for a specific time duration before proceeding.

**TimeSensor:**

Waits until a specific time of the day is reached.

**NamedHivePartitionSensor:**

Waits for a Hive partition to be created.

**HdfsSensor:**

Waits for a file or folder to exist on HDFS (Hadoop Distributed File System).

**DagRunSensor:**

Waits for a specific DagRun to be completed.
These sensors provide a flexible way to design workflows that depend on external events or conditions. You can choose the appropriate sensor based on the nature of the condition you are waiting for in your workflow.