# scout-scripts

# Prepare your local environment
* Clone the repo

  ```
  git clone https://github.com/oxhead/scout-scripts.git
  ```

* Insintall the scout benchmark tool

  ```
  cd scout-scripts
  virtualenv -p python3 .venv
  source .venv/bin/activate
  ```



## Prepare your AWS environment

* Create S3 buckets for benchmark results, e.g., scout-database

* Create a key pair for accessing (via SSH) the provision benchmark clusters, e.g., scout

* Create an IAM role to grant permission to instances.  The instance profile must have the permission to access EC2, S3 and EC2SpotFleet.  You will need its ARN, e.g., arn:aws:iam::169987671570:instance-profile/chin.

* Create an  IAM role for launch spot instances.  The IAM role must have the AmazonEC2SpotFleetTaggingRole permission.  You will need its ARN, e.g., arn:aws:iam::169987671570:role/aws-ec2-spot-fleet-tagging-role.

* Create a securaity group, e.g., scout, that allows communication among nodes and for you to connect to the master node.

* Prepare VPC

  * Create IPv4 CIDR, e.g. 10.0.0.0/16
  * Create a subnet and choose an available zone.  Specify 10.0.1.0/24 for the IPv4 CIDR block.



## Test Scout benchmarks

* Test the setup

  ```
  myaws run -w "hibench hadoop aggregation medium 1" --instance-type c4.large --instance-num 1 --keyname scout --cluster-mode single --s3-bucket scout-dataset-test --no-terminate --dry-run
  ```



## Scout Benchmarks

* The provide a prebuilt AMI.  Search **ami-2196095e** on the marketplace.

* The benchmark supports both the **single-node** and **multi-node** mode.

* We mailly test them with spot instances.  

* Commands

  ```
  myaws run
  	-w "hibench hadoop aggregation medium 1"
  	--keyname scout --s3-bucket scout-dataset-test
  	--instance-type c4.large --instance-num 1 --cluster-mode [single|n+1]
  	--volume-size 120
  	--terminate / --no-terminate
  	--dry-run / --no-dry-run
  ```

* Example 1: multiple workload with the single-node mode

  ```
  myaws run \
  	-w "hibench hadoop aggregation medium 1" \
  	-w "hibench hadoop wordcount large 1" \
  	--keyname scout --s3-bucket scout-dataset-test \
  	--instance-type c4.large --instance-num 1 --cluster-mode single \
  	--volume-size 120 \
  	--no-terminate \
  	--no-dry-run
  ```

* Example 2: multiple workloads with the multiple-node mode

  ```
  myaws run \
  	-w "hibench hadoop aggregation medium 1" \
  	--keyname scout --s3-bucket scout-dataset-test \
  	--instance-type c4.2xlarge --instance-num 16 --cluster-mode n+1 \
  	--volume-size 120 \
  	--no-terminate \
  	--no-dry-run
  ```

* The benchmark results are uploaded to the S3 bucket you specify.

  

## Supported Benchmark List

The folloing table shows the supported benchmark list.  In the command line above, you can specify valid workloads using this table.  The workload format is "benchmark framework app datasize benchmark_id".  Valid datasizes are small, medium, large, huge, bigdata.  You can specify your own benchmark_id.  For example, valid workloads are:

* "hibench hadoop aggregation medium 1"
* "hibench spark pagerank large 5"
* "sparkperf spark1.5 kmeans bigdata 10"

| benchmark | framework | app                   |
| --------- | --------- | --------------------- |
| hibench   | hadoop    | aggregation           |
| hibench   | hadoop    | dfsioe                |
| hibench   | hadoop    | join                  |
| hibench   | hadoop    | pagerank              |
| hibench   | hadoop    | scan                  |
| hibench   | hadoop    | sort                  |
| hibench   | hadoop    | terasort              |
| hibench   | spark     | aggregation           |
| hibench   | spark     | als                   |
| hibench   | spark     | bayes                 |
| hibench   | spark     | join                  |
| hibench   | spark     | lr                    |
| hibench   | spark     | pagerank              |
| hibench   | spark     | scan                  |
| hibench   | spark     | sort                  |
| hibench   | spark     | terasort              |
| hibench   | spark     | wordcount             |
| sparkperf | spark1.5  | als                   |
| sparkperf | spark1.5  | block-matrix-mult     |
| sparkperf | spark1.5  | chi-sq-feature        |
| sparkperf | spark1.5  | chi-sq-gof            |
| sparkperf | spark1.5  | chi-sq-mat            |
| sparkperf | spark1.5  | classification        |
| sparkperf | spark1.5  | decision-tree         |
| sparkperf | spark1.5  | fp-growth             |
| sparkperf | spark1.5  | gmm                   |
| sparkperf | spark1.5  | gradient-boosted-tree |
| sparkperf | spark1.5  | kmeans                |
| sparkperf | spark1.5  | lda                   |
| sparkperf | spark1.5  | naive-bayes           |
| sparkperf | spark1.5  | pca                   |
| sparkperf | spark1.5  | pearson               |
| sparkperf | spark1.5  | pic                   |
| sparkperf | spark1.5  | random-forest         |
| sparkperf | spark1.5  | regression            |
| sparkperf | spark1.5  | spearman              |
| sparkperf | spark1.5  | summary-statistics    |
| sparkperf | spark1.5  | svd                   |
| sparkperf | spark1.5  | word2vec              |

## Large-scale benchmark

* Do it only when you are confident about the scripts and the benchmark process.
* The python script at scripts/generate_dist_benchmark.py may give you a hint to create you own script.