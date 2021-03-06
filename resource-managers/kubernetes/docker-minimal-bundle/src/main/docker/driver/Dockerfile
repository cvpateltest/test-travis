FROM openjdk:8-alpine

# If this docker file is being used in the context of building your images from a Spark distribution, the docker build
# command should be invoked from the top level directory of the Spark distribution. E.g.:
# docker build -t spark-driver:latest -f dockerfiles/driver/Dockerfile .

RUN apk upgrade --update
RUN apk add --update bash
RUN mkdir -p /opt/spark
RUN touch /opt/spark/RELEASE

ADD jars /opt/spark/jars
ADD examples /opt/spark/examples
ADD bin /opt/spark/bin
ADD sbin /opt/spark/sbin
ADD conf /opt/spark/conf

ENV SPARK_HOME /opt/spark

WORKDIR /opt/spark

CMD SSL_ARGS="" && \
    if ! [ -z ${SPARK_SUBMISSION_USE_SSL+x} ]; then SSL_ARGS="$SSL_ARGS --use-ssl $SPARK_SUBMISSION_USE_SSL"; fi && \
    if ! [ -z ${SPARK_SUBMISSION_KEYSTORE_FILE+x} ]; then SSL_ARGS="$SSL_ARGS --keystore-file $SPARK_SUBMISSION_KEYSTORE_FILE"; fi && \
    if ! [ -z ${SPARK_SUBMISSION_KEYSTORE_TYPE+x} ]; then SSL_ARGS="$SSL_ARGS --keystore-type $SPARK_SUBMISSION_KEYSTORE_TYPE"; fi && \
    if ! [ -z ${SPARK_SUBMISSION_KEYSTORE_PASSWORD_FILE+x} ]; then SSL_ARGS="$SSL_ARGS --keystore-password-file $SPARK_SUBMISSION_KEYSTORE_PASSWORD_FILE"; fi && \
    if ! [ -z ${SPARK_SUBMISSION_KEYSTORE_KEY_PASSWORD_FILE+x} ]; then SSL_ARGS="$SSL_ARGS --keystore-key-password-file $SPARK_SUBMISSION_KEYSTORE_KEY_PASSWORD_FILE"; fi && \
    exec bin/spark-class org.apache.spark.deploy.rest.kubernetes.KubernetesSparkRestServer \
      --hostname $HOSTNAME \
      --port $SPARK_SUBMISSION_SERVER_PORT \
      --secret-file $SPARK_SUBMISSION_SECRET_LOCATION \
      ${SSL_ARGS}
