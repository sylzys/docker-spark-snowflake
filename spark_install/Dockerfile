FROM debian:bullseye-20220125 as builder
# Setting env veriables. REQ_VERSION must fit PYTHON_VERSION (Python 3.9 => REQ 39 etc)
ENV SPARK_VERSION=3.2.1 \
HADOOP_VERSION=3.2 \
PYTHON_VERSION=3.9 \
SNOWFLAKE_CONNECTOR_VERSION=2.7.4 \
REQ_VERSION=39 \ 
SPARK_HOME=/opt/spark \
JAVA_HOME=/usr

RUN apt-get update && apt-get install -y curl unzip wget cmake software-properties-common libssl-dev libffi-dev python3 python3-pip default-jre

RUN update-alternatives --install "/usr/bin/python" "python" "$(which python3)" 1
RUN python --version
RUN wget --no-verbose -O apache-spark.tgz "https://dlcdn.apache.org/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz" \
&& mkdir -p /opt/spark \
&& tar -xf apache-spark.tgz -C /opt/spark --strip-components=1 \
&& rm apache-spark.tgz

RUN python -m pip install --upgrade pip
RUN python -m pip install cmake pyarrow
RUN python -m pip install -r "https://raw.githubusercontent.com/snowflakedb/snowflake-connector-python/v${SNOWFLAKE_CONNECTOR_VERSION}/tested_requirements/requirements_${REQ_VERSION}.reqs"

RUN  python -m pip install "snowflake-connector-python==${SNOWFLAKE_CONNECTOR_VERSION}"

FROM builder as apache-spark

WORKDIR /opt/spark

ENV SPARK_MASTER_PORT=7077 \
SPARK_MASTER_WEBUI_PORT=8080 \
SPARK_MASTER_DNN=spark-master \ 
SPARK_LOG_DIR=/opt/spark/logs \
SPARK_MASTER_LOG=/opt/spark/logs/spark-master.log \
SPARK_WORKER_LOG=/opt/spark/logs/spark-worker.log \
SPARK_WORKER_WEBUI_PORT=8080 \
SPARK_WORKER_PORT=7000 \
SPARK_MASTER_DNN=spark\
SPARK_MASTER="spark://${SPARK_MASTER_DNN}:${SPARK_MASTER_PORT}" \
SPARK_MODE="master"

EXPOSE 8080 7077 7000

RUN mkdir -p $SPARK_LOG_DIR && \
touch $SPARK_MASTER_LOG && \
touch $SPARK_WORKER_LOG && \
ln -sf /dev/stdout $SPARK_MASTER_LOG && \
ln -sf /dev/stdout $SPARK_WORKER_LOG

COPY start-spark.sh /
# may be needed if file was written on a windows computer
RUN apt install -y dos2unix && dos2unix /start-spark.sh
CMD ["/bin/bash", "/start-spark.sh"]