FROM ataoge/ubuntu-server:latest
LABEL maintainer="taoh@chinadci.com"
ENV REFRESHED_AT 2019-03-28

# We can't use the standard ports 8080, 8443 in Web Adaptor
EXPOSE 80 443

ENV TOMCAT=tomcat8

RUN apt-get -y install openjdk-8-jre-headless

# In addtion to installing the server, this creates the tomcat8 user, group,
# and a non-user writable home directory at ${CATALINA_HOME} (see below)
RUN apt-get -y install ${TOMCAT}

# This is a workaround for a script bug in ESRI's configurewebadaptor.sh
# When we run that script it will want write permission on Tomcat's home directory
# and we can't give it access to /usr/share/tomcat8, that would be a security problem
# So we create a new home for it and then modify /etc/passwd to match.
# Hopefully changing Tomcat's home won't mess up tomcat itself.

ENV HOME=/home/${TOMCAT}
ENV CATALINA_HOME=/usr/share/${TOMCAT}
RUN mkdir ${HOME} && chown -R ${TOMCAT}.${TOMCAT} ${HOME} && usermod --home ${HOME} ${TOMCAT}

# This is only needed if you want to use the web gui to manage tomcat.
#RUN apt-get -y install ${TOMCAT}-admin
# FIXME should not define passwords in the file.
#RUN sed -i "s/<\/tomcat-users>/<user username=\"siteadmin\" password=\"changeit\" roles=\"manager-gui\"\/><\/tomcat-users>/" /etc/${TOMCAT}/tomcat-users.xml

# Note, there is a "tomcat8" string embedded in this script. This needs fixing.
ADD logrotate /etc/logrotate.d/${TOMCAT}
RUN chmod 644 /etc/logrotate.d/${TOMCAT}

# Change from port 8080 to port 80.
RUN sed -i "s/8080/80/" /etc/${TOMCAT}/server.xml
# Remove the redirect
RUN sed -i "s/redirectPort=\"8443\"//g" /etc/${TOMCAT}/server.xml

# Create and install a self-signed certificate.
RUN keytool -genkey -alias tomcat -keyalg RSA -keystore /etc/${TOMCAT}/.keystore \
    -storepass changeit -keypass changeit \
    -dname "CN=abc, OU=RDC, O=ChinaDCI, L=GZ, ST=GD, C=CN"
# Modify server.xml to activate the TLS service
RUN sed -i "s/<Service name=\"Catalina\">/<Service name=\"Catalina\">\n    <Connector port=\"443\" maxThreads=\"200\" scheme=\"https\" secure=\"true\" SSLEnabled=\"true\" keystorePass=\"changeit\" clientAuth=\"false\" sslProtocol=\"TLS\" keystoreFile=\"\/etc\/${TOMCAT}\/.keystore\" \/>/" \
        /etc/${TOMCAT}/server.xml

ENV PIDDIR=/var/run/${TOMCAT}
RUN mkdir ${PIDDIR} && chown ${TOMCAT}.${TOMCAT} ${PIDDIR}

# Set up authbind to allow tomcat to use ports 80 and 443
# (By default, non-privileged users are not allowed to use ports < 1024.)
ENV AUTHBIND=/etc/authbind/byport/
RUN touch ${AUTHBIND}/80 && touch ${AUTHBIND}/443
RUN chown ${TOMCAT} ${AUTHBIND}/80 ${AUTHBIND}/443
RUN chmod 755 ${AUTHBIND}/80 ${AUTHBIND}/443

WORKDIR ${HOME}
USER ${TOMCAT}

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ENV JSSE_HOME=${JAVA_HOME}/jre
ENV CATALINA_OUT=/var/log/${TOMCAT}/catalina.out
ENV CATALINA_TMPDIR=/tmp/${TOMCAT}
ENV CATALINA_PID=${PIDDIR}/${TOMCAT}.pid
ENV CATALINA_BASE=/var/lib/${TOMCAT}
# Child containers deploy WAR files here.
ENV CATALINA_APPS /var/lib/${TOMCAT}/webapps
# Set heap,memory options here
ENV CATALINA_OPTS="-Djava.awt.headless=true -Xmx128M"

RUN touch ${CATALINA_OUT} && mkdir ${CATALINA_TMPDIR}

HEALTHCHECK --interval=30s --timeout=10s --retries=3 CMD curl -sS 127.0.0.1 || exit 1

# Start Tomcat on low ports, running in foreground (don't daemonize)
CMD authbind --deep -c ${CATALINA_HOME}/bin/catalina.sh run
