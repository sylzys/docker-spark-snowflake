# Spark Cluster + Snowflake connector Docker Image

## Disclaimer

Currently, and still after many years, Apache Spark cannot use cluster deploy mode on standalone clusters. 
You can choose to remove the worker container for the docker-compose.yaml file, or leave it as is and submit your job without cluster mode.

I made the choice to keep both master and worker so jar app can be deployed with cluster mode if needed.

## Goals
This image install a development Spark cluster + a Python SnowFlake connector.

Current default versions are: 
- Spark: 3.2.1
- Hadoop: 3.2
- Python: 3.9
- Snowflake connector: 2.7.4

## Default configuration

2 containers will be deployed by default :

|  Name |  Exposed ports  |
|---|---|
|  spark_master |  8081 7077 |
|  spark_cluster| 8082 7078 |

A volume is created too:

|  local |  Container |
|---|---|
|  sparkapps |  /opt/spark/apps  |

## Customisation

To change containers names, ports, paths, volumes, please update variables values in docker-compose.yaml

## Snowflake credentials

For security reasons, Snowflake credentials are not hard coded.
To configure your connector, use the following command:

``` cp .env-example .env ```

and use your credentials. The corresponding values will be used during build and run.

## Installation

### Prerequisites

Please have Docker installed and running.

### Compose

The docker-compose.yaml file is made so that the image is automatically built. After cloning the repo, you can then directly run:

``` docker-compose up -d ``` to run the container in detached mode.

### Build / image customisation

If you change values in Dockerfile, you'll have to rebuild the image. You can use the following command:

``` docker-compose up -d --build ```

## Test your containers

To access Spark master, open:
[http://localhost:8081](http://localhost:8081)

For the worker, open: 

[http://localhost:8082](http://localhost:8082)

## Testing the connector

For a simple test, connect to the master or the worker, and run the following command:

``` python /opt/spark/validate.py ```

It should output the current snowflake version.

You can also submit the job without cluster mode: 

``` /opt/spark/bin/spark-submit /opt/spark/app/validate.py ```

# Happy coding !
