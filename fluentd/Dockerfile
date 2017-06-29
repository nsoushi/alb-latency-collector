FROM fluent/fluentd:v0.14.11

USER root
RUN ENTRYKIT_VERSION=0.4.0 && \
    apk --no-cache --update add bash tzdata && \
    apk --no-cache add --virtual=build-dependencies wget && \
    mkdir -p /tmp/src && \
    cd /tmp/src && \
    wget https://github.com/progrium/entrykit/releases/download/v${ENTRYKIT_VERSION}/entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz && \
    tar -xvzf entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz && \
    rm entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz && \
    mv entrykit /bin/entrykit && \
    chmod +x /bin/entrykit && \
    entrykit --symlink && \
    rm -rf /etc/localtime && \
    ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
    apk del build-dependencies && \
    rm -rf /tmp/src && \
    rm -rf /var/cache/apk/*

USER root
WORKDIR /home/fluent
COPY fluent-plugin-stats-0.3.6.gem ./
RUN gem install fluent-plugin-elasticsearch && \
    gem install fluent-plugin-record-reformer && \
    gem install -l fluent-plugin-stats-0.3.6.gem && \
    gem install fluent-plugin-mackerel && \
    gem install fluent-plugin-elb-log

COPY fluent.conf /fluentd/etc/fluent.conf
COPY conf.d /fluentd/etc/conf.d/

USER root
ENTRYPOINT [ \
    "render", \
        "/fluentd/etc/conf.d/api-log.conf", \
        "--", \
    "fluentd", "-c", "/fluentd/etc/fluent.conf", "-p", "/fluentd/plugins" ]
