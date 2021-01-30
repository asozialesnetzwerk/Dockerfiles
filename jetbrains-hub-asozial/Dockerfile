ARG HUB_VERSION=2020.1.12722
ARG IMAGE=jetbrains/hub:$HUB_VERSION
FROM $IMAGE
ARG PRODUCT=hub
ARG HUB_VERSION
USER root
RUN apt-get update \
 && apt-get install -y --no-install-recommends zip unzip \
 && cd /opt/$PRODUCT/apps/hub/web/WEB-INF/lib \
 && unzip jetbrains.jetpass.notification-$HUB_VERSION.jar templates/* \
 && sed -i 's/ nicht / /' templates/de/email-notification-layout.ftl \
 && sed -i 's/ do not / /' templates/en/email-notification-layout.ftl \
 && zip -rf jetbrains.jetpass.notification-$HUB_VERSION.jar templates \
 && rm -rf templates \
 && apt-get purge -y --autoremove zip unzip \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*
USER jetbrains
