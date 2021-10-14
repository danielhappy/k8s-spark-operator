# maven
FROM gcr.io/spark-operator/spark:v3.1.1
WORKDIR /tmp/

COPY . $WORKDIR

# spark
FROM datamechanics/spark:3.1-latest as service

USER root

ENV APP_HOME /opt/spark
ENV JARS=$SPARK_HOME/jars
ENV CONFIGS=$APP_HOME/config
ENV USER=1001

# dependencies
ENV POSTGRESQL_VERSION=42.2.19
ENV SNOWFLAKE_JDBC_VERSION=3.12.12
ENV H2_VERSION=1.4.200
ENV AWS_JAVA_SDK_VERSION=1.11.1034
ENV HADOOP_AWS_VERSION=3.3.1

WORKDIR $APP_HOME

# Specify the User that the actual main process will run as
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini

# remove pr-existing AWS JAR
RUN rm -rf $SPARK_HOME/jars/aws-java-sdk-bundle-*.jar

ADD https://repo1.maven.org/maven2/org/postgresql/postgresql/${POSTGRESQL_VERSION}/postgresql-${POSTGRESQL_VERSION}.jar $JARS/
ADD https://repo1.maven.org/maven2/net/snowflake/snowflake-jdbc/${SNOWFLAKE_JDBC_VERSION}/snowflake-jdbc-${SNOWFLAKE_JDBC_VERSION}.jar $JARS/
ADD https://repo1.maven.org/maven2/com/h2database/h2/${H2_VERSION}/h2-${H2_VERSION}.jar $JARS/
ADD https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/${AWS_JAVA_SDK_VERSION}/aws-java-sdk-bundle-${AWS_JAVA_SDK_VERSION}.jar $JARS/
ADD https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/${HADOOP_AWS_VERSION}/hadoop-aws-${HADOOP_AWS_VERSION}.jar $JARS/

RUN chmod +x /usr/bin/tini && echo "auth required pam_wheel.so use_uid" >> /etc/pam.d/su && chgrp root /etc/passwd && chmod ug+rw /etc/passwd && chmod ugo+rwx -R /opt/spark && chmod ugo+rwx -R $APP_HOME

USER ${USER}



ENV SPARK_DRIVER_CLASSPATH="$JARS/postgresql.jar,$JARS/snowflake-jdbc.jar,$JARS/h2.jar"

# just for testing
#CMD tail -f /dev/null
