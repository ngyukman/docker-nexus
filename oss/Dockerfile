FROM       centos:centos7
MAINTAINER Peter Ng <ngyukman@gmail.com>

ENV JAVA_VERSION 8u51
ENV JAVA_BUILD_VERSION b16
ENV SONATYPE_WORK /sonatype-work
ENV NEXUS_VERSION 3.0.0-m4
ENV NEXUS_REVISION 3.0.0-b2015061001

# Upgrading system
RUN yum -y upgrade

# Install Java
RUN cd /var/tmp \
  && curl --fail --silent --location --retry 3 -O \
  --header "Cookie: oraclelicense=accept-securebackup-cookie; " \
  http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}-${JAVA_BUILD_VERSION}/jdk-${JAVA_VERSION}-linux-x64.rpm \
  && rpm -Ui jdk-${JAVA_VERSION}-linux-x64.rpm \
  && rm -rf jdk-${JAVA_VERSION}-linux-x64.rpm

# Install base
RUN yum install -y \
  curl tar \
  && yum clean all

# Install Sonatype nexus 
RUN mkdir -p /opt/sonatype/nexus \
  && curl --fail --silent --location --retry 3 \
    https://download.sonatype.com/nexus/oss/nexus-${NEXUS_VERSION}-bundle.tar.gz \
  | gunzip \
  | tar x -C /tmp nexus-${NEXUS_REVISION} \
  && mv /tmp/nexus-${NEXUS_REVISION}/* /opt/sonatype/nexus/ \
  && rm -rf /tmp/nexus-${NEXUS_REVISION}

# Add repository user
RUN useradd -r -u 200 -m -c "nexus role account" -d ${SONATYPE_WORK} -s /bin/false nexus
RUN chown -R 200 /opt/sonatype/nexus

VOLUME ${SONATYPE_WORK}

EXPOSE 8082
WORKDIR /opt/sonatype/nexus
USER nexus
ENV APPLICATION_PORT 8082
ENV JAVA_MAX_MEM 768M
ENV JAVA_MIN_MEM 256M
ENV EXTRA_JAVA_OPTS -Dnexus-work=${SONATYPE_WORK} -Dapplication-port=${APPLICATION_PORT}
CMD bin/nexus console
