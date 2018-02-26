#
# Creates a docker container with SonarQube, incl. several plugins
#

FROM openjdk:8

#MAINTAINER Oleksandr Shtalinberg

#ENV JAVA_OPTS "$JAVA_OPTS -Duser.country=DE -Duser.language=de -Duser.timezone=Europe/Berlin"

# Set the JAVA_HOME variable to make it clear where Java is located
#ENV JAVA_HOME /usr/lib/jvm/java-1.8.0

ENV SONAR_VERSION 6.7.1
ENV SONARQUBE_HOME /opt/sonarqube
# Database configuration
# Defaults to using H2
#ENV SONARQUBE_JDBC_USERNAME=sonar
#ENV SONARQUBE_JDBC_PASSWORD=sonar
#ENV SONARQUBE_JDBC_URL=

# Plugin Versions
# ENV SONAR_JAVA_PLUGIN 4.2
ENV SONAR_WEB_PLUGIN 2.4
ENV SONAR_SCM_GIT_PLUGIN 1.0

RUN INSTALL_PKGS="java-1.8.0-openjdk.x86_64" && \
    yum -y --setopt=tsflags=nodocs install $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all  && \
    localedef -f UTF-8 -i en_US en_US.UTF-8

# Http port
EXPOSE 9000

# H2 Database port
EXPOSE 9092

RUN groupadd -r sonarqube && useradd -r -g sonarqube sonarqube

# # Install packages necessary
# RUN yum -y install unzip && yum clean all

# # grab gosu for easy step-down from root
# RUN set -x \
#     && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.10/gosu-$(dpkg --print-architecture)" \
#     && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/1.10/gosu-$(dpkg --print-architecture).asc" \
#     && export GNUPGHOME="$(mktemp -d)" \
#     && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
#     && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
#     && rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc \
#     && chmod +x /usr/local/bin/gosu \
#     && gosu nobody true


RUN set -x \

    # pub   2048R/D26468DE 2015-05-25
    #       Key fingerprint = F118 2E81 C792 9289 21DB  CAB4 CFCA 4A29 D264 68DE
    # uid                  sonarsource_deployer (Sonarsource Deployer) <infra@sonarsource.com>
    # sub   2048R/06855C1D 2015-05-25
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys F1182E81C792928921DBCAB4CFCA4A29D26468DE \

    && cd /opt \
    && curl -o sonarqube.zip -fSL https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-$SONAR_VERSION.zip \
    && curl -o sonarqube.zip.asc -fSL https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-$SONAR_VERSION.zip.asc \
    && gpg --batch --verify sonarqube.zip.asc sonarqube.zip \
    && unzip sonarqube.zip \
    && mv sonarqube-$SONAR_VERSION sonarqube \
    && chown -R sonarqube:sonarqube sonarqube \
    && rm sonarqube.zip* \
    && rm -rf $SONARQUBE_HOME/bin/*


# Add SonarQube binaries from Nexus Repository
#ADD https://sonarsource.bintray.com/Distribution/sonarqube/sonarqube-${SONAR_VERSION}.zip $SONARQUBE_HOME/sonarqube-${SONAR_VERSION}.zip

# Unpack SonarQube Zip
#RUN set -x \
#	&& unzip $SONARQUBE_HOME/sonarqube-${SONAR_VERSION}.zip \
#	&& mv sonarqube-${SONAR_VERSION}/* $SONARQUBE_HOME \
#	&& rm $SONARQUBE_HOME/sonarqube-${SONAR_VERSION}.zip

# Add plugins
RUN mkdir -p $SONARQUBE_HOME/extensions/plugins/
#ADD http://central.maven.org/maven2/org/sonarsource/java/sonar-java-plugin/${SONAR_JAVA_PLUGIN}/sonar-java-plugin-${SONAR_JAVA_PLUGIN}.jar $SONARQUBE_HOME/extensions/plugins/sonar-java-plugin-${SONAR_JAVA_PLUGIN}.jar
ADD http://central.maven.org/maven2/org/sonarsource/sonar-web-plugin/sonar-web-plugin/${SONAR_WEB_PLUGIN}/sonar-web-plugin-${SONAR_WEB_PLUGIN}.jar $SONARQUBE_HOME/extensions/plugins/sonar-web-plugin-${SONAR_WEB_PLUGIN}.jar
ADD http://central.maven.org/maven2/org/codehaus/sonar-plugins/sonar-scm-git-plugin/${SONAR_SCM_GIT_PLUGIN}/sonar-scm-git-plugin-${SONAR_SCM_GIT_PLUGIN}.jar $SONARQUBE_HOME/extensions/plugins/sonar-scm-git-plugin-${SONAR_SCM_GIT_PLUGIN}.jar
#COPY run.sh $SONARQUBE_HOME

WORKDIR $SONARQUBE_HOME

VOLUME ["$SONARQUBE_HOME/data","$SONARQUBE_HOME/conf","$SONARQUBE_HOME/logs"]

#COPY run.sh $SONARQUBE_HOME/bin/
#ENTRYPOINT ["./bin/run.sh"]

RUN chmod -R 777 $SONARQUBE_HOME

CMD ["/opt/sonarqube/run.sh"]
