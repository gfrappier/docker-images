ARG BASE_IMAGE

FROM $BASE_IMAGE as builder

ARG CORE_NAMES
ARG MANAGED_SCHEMA_DEFAULT_NAME

COPY ${MANAGED_SCHEMA_DEFAULT_NAME} /opt/managed-schema

USER root

RUN set -e; \
    CORES_DIR='/opt/solr/server/solr'; \
    ORIG_IFS=${IFS}; \
    IFS=', '; \
        for core in ${CORE_NAMES}; do \
        echo "Creating '$CORES_DIR/$core'..."; \
        cp -r /opt/solr/server/solr/configsets/_default/ $CORES_DIR/$core; \
        cp /opt/managed-schema $CORES_DIR/$core/conf/managed-schema; \
        touch "$CORES_DIR/$core/core.properties"; \
    done; \
    IFS=${ORIG_IFS};

FROM $BASE_IMAGE

COPY --from=builder ["/opt/solr/server/solr", "/clean"]
COPY boot.sh /opt/boot.sh

USER root

RUN chmod +x /opt/boot.sh && \
    chown -R $SOLR_USER:$SOLR_USER /clean && \
    chmod -R 700 /clean && \
    mkdir -p /data && \
    chown -R $SOLR_USER:$SOLR_USER /data && \
    chmod -R 700 /data

USER $SOLR_USER

ENV SOLR_HOME=/data

ENTRYPOINT ["/opt/boot.sh", "/clean", "/data"]
