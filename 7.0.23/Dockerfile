FROM eclipse-temurin:17.0.14_7-jdk

LABEL org.opencontainers.image.base.name="eclipse-temurin:17.0.14_7-jdk"
LABEL org.opencontainers.image.source="https://github.com/eclipse-ee4j/glassfish.docker"
LABEL org.opencontainers.image.url="https://github.com/eclipse-ee4j/glassfish.docker/wiki"

LABEL org.opencontainers.image.title="Eclipse GlassFish"
LABEL org.opencontainers.image.description="The Official Eclipse GlassFish Docker Image"
LABEL org.opencontainers.image.version="7.0.23"

LABEL org.opencontainers.image.authors="glassfish-dev@eclipse.org"
LABEL org.opencontainers.image.vendor="Eclipse Foundation"
LABEL org.opencontainers.image.licenses="EPL-2.0"

EXPOSE 4848 9009 8080 8181 7676 8686 3700 3820 3920 6666

# You should use own credentials and own files! These are just defaults.
ARG AS_ADMIN_PASSWORD=admin \
    PATH_GF_PASSWORD_FILE_FOR_CHANGE=/password-change.txt \
    UID=1000 \
    GID=1000
ENV PATH_GF_HOME=/opt/glassfish7 \
    AS_ADMIN_USER=admin \
    AS_ADMIN_PASSWORDFILE=/password.txt
ENV AS_USER=${AS_ADMIN_USER} \
    AS_PASSWORD_FILE=${AS_ADMIN_PASSWORDFILE} \
    AS_TRACE=false \
    AS_TRACE_LOGGING=false \
    AS_TRACE_BOOTSTRAP=false \
    AS_STOP_TIMEOUT=9500 \
    GLASSFISH_DOWNLOAD_SHA512=842850b2dd6f810cc78381f3b251709507902194f0d76a01ae74e41ebdf651127278e1df8f25f9c4db409dc6c512b9b87459d00c5bbc4bfcd02751983150e722 \
    GLASSFISH_VERSION=7.0.23 \
    PATH_GF_BIN=${PATH_GF_HOME}/bin \
    PATH_GF_SERVER_LOG="${PATH_GF_HOME}/glassfish/domains/domain1/logs/server.log"
ENV PATH="${PATH_GF_BIN}:${PATH}"

COPY 7.0.23/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh

RUN true \
    && set -x \
    && apt update \
    && apt upgrade -y \
    && apt install -y gpg unzip \
    && curl -fL "https://repo1.maven.org/maven2/org/glassfish/main/distributions/glassfish/${GLASSFISH_VERSION}/glassfish-${GLASSFISH_VERSION}.zip.asc" -o glassfish.zip.asc \
    && curl -fL "https://repo1.maven.org/maven2/org/glassfish/main/distributions/glassfish/${GLASSFISH_VERSION}/glassfish-${GLASSFISH_VERSION}.zip" -o glassfish.zip \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys D4A77129F00F736293BE5A51AFC18A2271EDDFE1 \
    && gpg --batch --verify glassfish.zip.asc glassfish.zip \
    && rm glassfish.zip.asc \
    && echo "$GLASSFISH_DOWNLOAD_SHA512 glassfish.zip" | sha512sum --strict --check \
    && mkdir -p "${PATH_GF_HOME}" \
    && unzip -q glassfish.zip -d "${PATH_GF_HOME}/.." \
    && rm glassfish.zip \
    && userdel -r ubuntu \
    && groupadd -g ${GID} glassfish \
    && useradd -r -l -u ${UID} -g ${GID} -d "${PATH_GF_HOME}" -s /bin/bash glassfish \
    && echo "Generating password file at ${AS_PASSWORD_FILE} ..." \
    && set +x \
    && echo "AS_ADMIN_PASSWORD=${AS_ADMIN_PASSWORD}" > "${AS_PASSWORD_FILE}" \
    && chown glassfish:glassfish "${AS_PASSWORD_FILE}" \
    && echo "AS_ADMIN_PASSWORD=" > "${PATH_GF_PASSWORD_FILE_FOR_CHANGE}" \
    && echo "AS_ADMIN_NEWPASSWORD=${AS_ADMIN_PASSWORD}" >> "${PATH_GF_PASSWORD_FILE_FOR_CHANGE}" \
    && echo "" >> "${PATH_GF_PASSWORD_FILE_FOR_CHANGE}" \
    && unset AS_ADMIN_PASSWORD \
    && set -x \
    && env | sort \
    && AS_START_TIMEOUT=120000 asadmin start-domain \
    && curl  -o /dev/null http://localhost:4848 \
    && asadmin --passwordfile ${PATH_GF_PASSWORD_FILE_FOR_CHANGE} change-admin-password \
    && asadmin stop-domain --kill \
    && AS_START_TIMEOUT=120000 asadmin start-domain \
    && curl  -o /dev/null http://localhost:4848 \
    && asadmin set-log-attributes org.glassfish.main.jul.handler.GlassFishLogHandler.enabled=false \
    && asadmin set-log-attributes org.glassfish.main.jul.handler.SimpleLogHandler.level=FINEST \
    && asadmin enable-secure-admin \
    && asadmin stop-domain --kill \
    && rm -f ${PATH_GF_SERVER_LOG} ${PATH_GF_PASSWORD_FILE_FOR_CHANGE} \
    && chown -R glassfish:glassfish "${PATH_GF_HOME}" \
    && chmod +x /usr/local/bin/docker-entrypoint.sh \
    && mkdir ${PATH_GF_HOME}/autodeploy \
    && echo "Installation was successful."

# Add Oracle JDBC Drivers and Wallet
COPY --chown=glassfish:glassfish ojdbc10-full/ojdbc10.jar ojdbc10-full/oraclepki.jar ojdbc10-full/osdt_core.jar ojdbc10-full/osdt_cert.jar ojdbc10-full/ucp.jar /opt/glassfish7/glassfish/lib/
COPY --chown=glassfish:glassfish wallet /opt/oracle/wallet/

# Configure JDBC Connection Pool and Resource for Oracle Wallet
# Note: Using default admin password 'admin' assumed to work during build after initial setup above.
# Storing DB password in alias 'nimbledb_password_alias'.
RUN echo "AS_ADMIN_ALIASPASSWORD=Hastael125687" > /tmp/dbpwd && \
    asadmin start-domain && \
    asadmin create-password-alias nimbledb_password_alias --passwordfile /tmp/dbpwd && \
    asadmin create-jdbc-connection-pool \
        --datasourceclassname oracle.jdbc.pool.OracleDataSource \
        --restype javax.sql.DataSource \
        --ping=true \
        --steadypoolsize=8 \
        --maxpoolsize=32 \
        --poolresize=2 \
        --idletimeout=300 \
        --maxwait=60000 \
        --statementtimeout=-1 \
        --statementcachesize=0 \
        --validateatmostonceperiod=0 \
        --leaktimeout=0 \
        --leakreclaim=false \
        --creationretryattempts=0 \
        --creationretryinterval=10 \
        --isisolationguaranteed=true \
        --isconnectvalidatereq=true \
        --validationmethod=table \
        --validationtable DUAL \
        --failconnection=true \
        --nontransactionalconnections=false \
        --lazyconnectionassociation=false \
        --lazyconnectionenlistment=false \
        --associatewiththread=false \
        --matchconnections=false \
        --maxconnectionusagecount=0 \
        --wrapjdbcobjects=true \
        --pooling=true \
        --validationclassname="" \
        --allownoncomponentcallers=false \
        --driverclassname="" \
        --property "URL='jdbc:oracle:thin:@nimbledb_high?TNS_ADMIN=/opt/oracle/wallet':User='ADMIN':Password='\${ALIAS=nimbledb_password_alias}':oracle.net.wallet_location='/opt/oracle/wallet':ssl='true':MaxStatements='0':ImplicitCachingEnabled='false':NetworkProtocol='tcp':DataSourceName='OracleDataSource':LoginTimeout='0':PortNumber='0':ExplicitCachingEnabled='false'" \
        NimbleDBPool && \
    asadmin create-jdbc-resource \
        --connectionpoolid NimbleDBPool \
        --enabled=true \
        jdbc/NimbleDS && \
    asadmin stop-domain && \
    rm /tmp/dbpwd && \
    # Ensure correct ownership again after modifications
    chown -R glassfish:glassfish /opt/glassfish7/glassfish/domains/domain1 && \
    chown -R glassfish:glassfish /opt/oracle/wallet

USER glassfish
WORKDIR ${PATH_GF_HOME}
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["startserv"]
