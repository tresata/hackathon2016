Hackathon 2016
==============

Welcome to hackathon 2016!

Download the kickoff deck for more information on the business problem, the hackathon agenda, the data, and more! Find it in the data folder: [Hackathon 2016 kickoff deck](https://github.com/tresata/hackathon2016/blob/master/data/hackathon_kickoff_deck2016.pdf)

Table of Content: 

* [Getting Started](https://github.com/tresata/hackathon2016#getting-started)
* [Machines](https://github.com/tresata/hackathon2016#machines)
* [HDFS] (https://github.com/tresata/hackathon2016#hdfs)
* [Data] (https://github.com/tresata/hackathon2016#data)
* [Hive] (https://github.com/tresata/hackathon2016#hive)
* [Spark] (https://github.com/tresata/hackathon2016#spark)
* [pySpark] (https://github.com/tresata/hackathon2016#pyspark)
* [Anaconda] (https://github.com/tresata/hackathon2016#anaconda)
* [Scalding] (https://github.com/tresata/hackathon2016#scalding)
* [Tresata ORION] (https://github.com/tresata/hackathon2016#tresata-orion)
* [Resource Manager] (https://github.com/tresata/hackathon2016#resource-manager)

## Getting Started

You can obtain a username and login information from one of the Tresata representatives.

Edit your hosts file and add a line to associate the ip to the hackathon hostname:

    <ip-address> <host-name>

[Here](https://support.rackspace.com/how-to/modify-your-hosts-file/) is the helpful link for editing your hosts file for Windows, Mac and Linux

Ssh into a server where you can access the retail data stored on the hackathon Hadoop cluster.

    > ssh <username>@<ip-address>

and enter the password you were given.

We made Hive, Spark, pySpark and Anaconda command-line interfaces available, and included a tool to compile and run simple Scalding scripts on-the-fly.

## Machines

We have a Hadoop cluster with one master and four slaves. The slaves have 32 cores, 14 1TB data drives, and 128GB of RAM. You will have ssh access to the slaves.

Please spread yourselves out across the machines.

## HDFS
To access your HDFS location, you need to use hadoop fs commands (some reference: http://www.folkstalk.com/2013/09/hadoop-fs-shell-command-example-tutorial.html). For example, to take a look at your home directory on HDFS, use

    > hadoop fs -ls

or

    > hadoop fs -ls /user/username

## Data

You can find the data on HDFS in the /data folder, which contains the full data set with/without headers, as well as a 1% sample with/without headers.

    /data/full-dataset
    /data/full-dataset-without-header
    /data/sample-dataset
    /data/sample-dataset-without-header
    


The sample files are also available on the local file system:

    /home/shared/


## Hive

In hive the following tables are available:

    distribution
    distribution_orc
    distribution_sample
    donations
    donations_orc
    donations_sample
    ht_transactions
    ht_transactions_sample_orc
    ht_transactions_sample
    monetary_donations
    monetary_donations_orc
    monetary_donations_sample

Give Hive a whirl and run a sample query:

    > hive

Try pasting the following query into the hive command-line interface:

    hive> show tables;
    OK
    distribution
    distribution_orc
    distribution_sample
    donations
    donations_orc
    donations_sample
    ht_transactions
    ht_transactions_sample_orc
    ht_transactions_sample
    monetary_donations
    monetary_donations_orc
    monetary_donations_sample
    Time taken: 1.168 seconds, Fetched: 12 row(s)

    hive> select * from ht_transactions limit 10;

This will return all the fields for the first ten items in the 'ht_transactions' table.

If you'd like to create a file from the command, you can use a create table command:

    hive> create table test row format delimited fields terminated by '|' stored as textfile as select * from default.ht_transactions limit 10;

You can then extract the table from the hive warehouse for a table named test:

    hadoop fs -text /user/hive/warehouse/test/*.snappy > textfile

## Spark

**Spark-shell** can be found at /usr/local/lib/spark/bin

Now give the Spark-shell a test:

    > /usr/local/lib/spark/bin/spark-shell --num-executors 4 --executor-cores 1 --executor-memory 1G


Read in the data and run a simple query that calcuates the unique count of Donor_ID:

    val df = sqlContext.read.parquet("/data/full-dataset-parquet/donations.parquet")
    df.groupBy("Donor_ID").count().collect()


Note that for your "production" run on the full dataset you might want to increase resources used on the cluster:

    --num-executors 2 --executor-cores 2 --executor-memory 2G

Keep in mind that a spark-shell takes up these resources on the cluster even when you do not use them so please do not keep a spark-shell with "production" resources open unused.  


## pySpark

**pySpark** can be found at /usr/local/lib/spark/bin


You can also do the same query using a python version of the Spark shell.

    >  /usr/local/lib/spark/bin/pyspark --num-executors 4 --executor-cores 1 --executor-memory 1G

Read in the data and run a simple query that calcuates the unique count of Donor_ID:

    df = sqlContext.read.parquet("/data/full-dataset-parquet/donations.parquet")
    df.groupBy("Donor_ID").count().collect()

Note that for your "production" run on the full dataset you might want to increase resources used on the cluster:

    --num-executors 2 --executor-cores 2 --executor-memory 2G

Keep in mind that a pyspark takes up these resources on the cluster even when you do not use them so please do not keep a pyspark shell (interpreter) with "production" resources open unused.  


## Anaconda
Anaconda is a completely free Python distribution from [Continuum Analytics](https://www.continuum.io). It includes more than 400 of the most popular Python packages for science, math, engineering, and data analysis. See [the packages included with Anaconda](http://docs.continuum.io/anaconda/pkg-docs).


Getting failimar with conda: http://conda.pydata.org/docs/using/index.html


## Scalding

In addition to the Hive and Spark shells, we're also packaging Eval-tool and df-eval-tool, a tool to compile and run Scalding scripts without having to create a project. If you create a file called test.scala with the following contents:

    import com.twitter.scalding._
    import com.tresata.scalding.Dsl._
    import com.tresata.scalding.util.ScaldingUtil

    (args: Args) => {
      new Job(args) {
        ScaldingUtil.sourceFromArg(args("input"))
          
          .write(ScaldingUtil.sourceFromArg(args("output")))
      }
    }

you can run a query on the data set sample from the command-line:

    > eval-tool test.scala --hdfs --input bsv%/data/

This will generate a bar-separated file called 'upc_counts' in your HDFS home directory, containing the upc numbers along with their total counts.

df-eval-tool

    spark_eval_example.scala
    import com.twitter.scalding.Args
    import com.tresata.spark.sql.Job
    import com.tresata.spark.sql.source.Source

    (args : Args) =>
      new Job(args) {
        override def run = {
          val fapi = Source.fromArg(args, "input").read(minPartitions = 1000).fieldsApi
    
          fapi
            .groupBy('Donor_ID) { _
              .size('Donation_Count)
            }
            .write(Source.fromArg(args, "output"))
        }
      }

run df-eval-tool

    > df-eval-tool test.scala --input bsv%donations.bsv --output bsv%donation_counts.bsv

## Tresata ORION

ORION provides the one intelligence discovery application every data scientist is currently dreaming about. And when fed with data that holds connections between unique entities, it comes into its own as a business.

Explore unseen connections, uncovering intelligence that might be the missing link to the bigger story behind your data. ORION is a relationship discovery, traversal and querying engine that has the ability to mine, represent and algorithmically process any network dynamic in real time.

Orion can we accessed via http://hack01:8082.

For login, please contact the Orion room on Slack

Example Queries can be found [here](https://github.com/tresata/hackathon2016/blob/master/data/orion_queries.pdf)

## Resource Manager

http://hack01:8088/

