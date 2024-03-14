# Summary

**Due Date** -- April 14, 2021, 11:59 PM. This due date will be strictly enforced and penalties issued if you dont submit in time. 

## Goals

- Learn to set up a distributed cluster for spark
- Lear to use apache Yarn as resource manager.
- Learn to program with Spark using the pyspark python library
- Deploy pyspark script in cluster mode on AWS EMR. 

## Please note that we have provided [example_emr_spark_wordcount_example.ipynb](example_emr_spark_wordcount_example.ipynb) as an example. This will allow you to ensure that your emr connection and credentials are working correctly.

## Your tasks

Complete the following notebooks

* [spark_jobs.ipynb](spark_jobs.ipynb)

# Description

## Prior Reading

**Note** -- remember to update the paths of all notebooks to your homework repository url. Or use the colab plugin to open the notebook.

- [Running Spark on Yarn](http://spark.apache.org/docs/latest/running-on-yarn.html)
- [Installing Spark on Ubuntu](https://brightspace.vanderbilt.edu/d2l/le/content/269528/Home)
- [Apache Spark Applications with Amazon EMR and S3 Services using Jupyter Notebook](https://medium.com/analytics-vidhya/apache-spark-applications-with-amazon-emr-and-s3-services-using-jupyter-notebook-41968a1c2d7)
- [A tale of Spark Session and Spark Context](https://medium.com/@achilleus/spark-session-10d0d66d1d24)

Also take a look at the spark examples at https://github.com/vu-topics-in-big-data-2022/examples/tree/main/example-spark.

## Description of Cloud Technology Used

### Spark Core
Spark Core is the underlying general execution engine for the Spark platform that all other functionality is built on top of. It provides in-memory computing capabilities to deliver speed, a generalized execution model to support a wide variety of applications, and Java, Scala, and Python APIs for ease of development.


### Apache Yarn
YARN is one of the core components of the open-source [Apache Hadoop](https://intellipaat.com/blog/tutorial/hadoop-tutorial/) distributed processing framework. It helps in scheduling and resource management of the distributed cluster. YARN was initially called 'MapReduce 2' since it took the original [MapReduce](https://intellipaat.com/blog/what-is-mapreduce/) to another level by decoupling MapReduce resource management and scheduling capabilities from the data processing unit.

## Dataset Description
We'll use three datasets as inputs:
- [Tweets Dataset](./data/Tweets%20Dataset/nashville-tweets-2019-01-28.zip)
- Baseball Dataset [Batting File](./data/Baseball%20Dataset/Batting.csv.zip)
- Baseball Dataset [Salaries File](./data/Baseball%20Dataset/Salaries.csv.zip)

Note all these datasets are compressed. So to use them you will have to unzip them. Also ensure that you read the readme file of each dataset first.


**Note** - delete your EMR cluster when you are not using it. Do not let it sit idle for long time. It will use the results.

## Setup Instructions

Spark API provides three deployment modes: local, client, and cluster. 

***The first exercise*** of this assignment requires you to complete three MapReduce tasks under Spark [cluster mode](https://spark.apache.org/docs/latest/cluster-overview.html). We will use [Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) as the cluster manager. **Note** - you should first show that your code works in the local mode of spark as shown in the class examples. Only when that works, you should try the spark on EMR. If the spark on EMR fails, submit the local version of execution and corresponding code as part of the assignment. Put enough information in your submission to explain what went wrong with EMR submission.

The overall deployment architecture is shown below:

![arch](./images/arch.png)

Please watch the [instruction video](https://www.youtube.com/watch?v=w4HNnKuAgcA) for setting up the EMR cluster and executing a simple [word count example](./emr_spark_wordcount_example.ipynb). Please make sure you conducted the following steps:

- Step 1. Create S3 buckets and folders for storing EMR logs and data files

<img src="./images/setup_emr/step1.png" alt="step1" style="zoom:50%;" />

- Step 2. Create the EMR cluster. Choose configuration options as shown below. Remember to click on Advanced Options to switch to this view.

  <img src="./images/setup_emr/step2-1.png" alt="step2" style="zoom:50%;" />

  - Configure cluster node and instances as below. Please create at least three instances.(1 Master and 2 Workers)

  <img src="./images/setup_emr/step2-2.png" alt="	" style="zoom:50%;" />

  - Select the S3 folder you created for EMR logging

  <img src="./images/setup_emr/step2-3.png" alt="step2-3" style="zoom:50%;" />

  - Make sure you select the ec2 key pair when configuring security option.

*...... Wait for the cluster to be ready (about 5 minutes) ....*

## Exercises
This assignment contains two exercises.

## Exercise 1 (100 points)
The first requires you to read a collection of lines from data files to create RDDs and complete tasks in the [spark_jobs](./spark_jobs.ipynb) notebook. Note that you can test this program locally in colab first as shown in https://github.com/vu-topics-in-big-data-2021/examples/tree/main/example-spark and then connect to the spark cluster in EMR -- see the two methods colab+EMR or EMR Notebook described below. The idea is that you will report the difference in execution times (compared to running it just on colab and running on EMR).


### Exercise 2 (20 points - bonus)

Investigate what is spark streaming and write a report, 1-page, providing some examples on when spark streaming should be used. Submit this report as a pdf in your github repository. Call it excercise2-report.pdf

### Step1. Create a boto3 session using your AWS credentials, which allows you to access AWS resources from colab.

```python
import boto3

credentials = {
    'region_name': 'xxx',
    'aws_access_key_id': 'xxx',
    'aws_secret_access_key': 'xxx',
    'aws_session_token': 'xxx'
}

session = boto3.session.Session(**credentials)
```

### Step2. Upload data files to S3 through web console or boto3 API. If you want to use boto3 API, please upload them to Colab firstly.

### Step3. Complete programming tasks in the notebook, save as python scripts, and upload them to S3 bucket.

```python
# the %%file magic command automatically saves contents in the cell to a python script
# for example, the below command will produce the 1_count_streaming.py file
%%file 1_count_streaming.py

...

s3 = session.client('s3')  # create a S3 resource client

# Then, upload the python script to S3
# Note this step is needed only if you submit spark jobs to EMR from Colab.
# If you prefer testing spark jobs using EMR Notebook(see Step4), please skip the skip.
"""
Upload a file to an S3 bucket

:param file_name: File to upload
:param bucket: Bucket to upload to
:param object_name: S3 object name. If not specified then file_name is used
:return: True if file was uploaded, else False
"""

s3.upload_file(Filename='<dumped python script>', Bucket='<s3 bucket name>', Key='<s3 object key of this script>')
```

Note you can use %%file magic to save the file.

### Step4. Test Spark/Spark streaming application program on Colab and EMR.

#### To save AWS credits, please test your implementation on Colab firstly.	

```sh
# for example
!spark-submit --packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.4.7 1_count.py
```

###### What is happening?

The above command links required maven coordinates of jars and executes a PySpark application program(`1_count.py` in this example)  using the `spark-submit` command that is stored in Spark's bin directory. Spark is installed on Colab VM when you execute the first cell of each notebook.

```python
!apt-get install openjdk-8-jdk-headless -qq > /dev/null
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-8-openjdk-amd64"

#install spark
!wget -q https://downloads.apache.org/spark/spark-3.2.1/spark-3.2.1-bin-hadoop3.2.tgz
!tar xf spark-3.2.1-bin-hadoop3.2.tgz
!ls -l

os.environ["SPARK_HOME"] = "spark-3.2.1-bin-hadoop3.2"

!pip install -q findspark pyspark
import findspark
#Provides findspark.init() to make pyspark importable as a regular library.
findspark.init()
```

#### There are two options to test spark jobs on EMR.

*Option 1: Colab + EMR*

With this option, you need to use boto3 API to connect to EMR then call the `add_job_flow_steps` function to deploy jobs on remote EMR cluster. The provided notebooks have automate the process of submiting jobs and testing execution results.

```python
# replae with your EMR cluster ID
CLUSTER_ID = 'j-xxxxxxx'

def submit_job(app_name, pyfile_uri):
  	"""
  	:param app_name: name of the submited spark application
  	:param pyfile_uri: S3 object URI of the PySpark application you upload in step 3
  	"""
    emr = session.client('emr') # the session was created in Step 1
    emr.add_job_flow_steps(JobFlowId=CLUSTER_ID, Steps=[{
        'Name': app_name,
        'ActionOnFailure': 'CANCEL_AND_WAIT',
        'HadoopJarStep': {
            'Args': ['spark-submit',
                     '--master', 'yarn',
                     '--deploy-mode', 'cluster',
                     '--packages', 'org.apache.spark:spark-streaming-kafka-0-8_2.11:2.4.7',
                     pyfile_uri],
            'Jar': 'command-runner.jar'
        }}])

# Please call this function when you are ready to submit your program to EMR.
```

*Option 2: EMR Notebook*

Alternatively, you can create EMR notebooks, **copy the contents of python scripts and test functions to notebook cells manually **, and execute jobs with a PySpark notebook kernel. The below shows an example of creating an EMR notebook.

**Note:** 

1. Your cluster should be in `Waiting` or `Running` state before creating the notebook.
2. You are free to use other S3 location to store the notebook.
3. Git integration is not available since our repositories are private.
4. Leave other configurations as same as the example.

 <img src="./images/setup_emr/emr_notebook.png" alt="emr_notebook" style="zoom:50%;" />

Once the notebook is created, **please change Kernel Type to PySpark.**

- The PySpark kernel creates two entry points: SparkSession(spark) and SparkContext(sc).
- You can use 'spark' or 'sc' to define, access, and execute your spark application programs.

When you start executing cells in the notebook, the kernel will automatically start a spark application, like below:

![start_spark](./images/setup_emr/start_spark.png)

EMR owns full access to S3 buckets, thus you can simply use S3 object URI to read/write files. For example, the following shows how to execute the [emr_spark_wordcount_example](emr_spark_wordcount_example.ipynb) on EMR notebook:

<img src="./images/setup_emr/example-1-1.png" alt="example" style="zoom:50%;" />

Then read the output file from S3 as text. 

<img src="./images/setup_emr/example-1-2.png" alt="example" style="zoom:50%;" />

Finally,  download and save notebooks back to Github.

**IMPORTANT: Please TERMINATE your EMR cluster, EC2 instance, and clean up S3 once the homework is done.**
