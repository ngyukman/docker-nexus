FROM       java:8-jre
MAINTAINER Peter Ng <ngyukman@gmail.com>

ENV SONATYPE_WORK /sonatype-work
ENV NEXUS_VERSION 3.0.0-m4
ENV NEXUS_REVISION 3.0.0-b2015061001
ENV NEXUS_PORT 8081

# Install base
RUN apt-get install curl tar

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

EXPOSE ${NEXUS_PORT}
WORKDIR /opt/sonatype/nexus
USER nexus
ENV APPLICATION_PORT ${NEXUS_PORT}
ENV JAVA_MAX_MEM 768M
ENV JAVA_MIN_MEM 256M
ENV EXTRA_JAVA_OPTS -Dnexus-work=${SONATYPE_WORK} -Dapplication-port=${APPLICATION_PORT}
CMD bin/nexus console
