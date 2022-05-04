# syntax=docker.io/docker/dockerfile:1.4
ARG VERSION
ARG IMAGE=docker.io/jetbrains/hub:$VERSION
FROM $IMAGE
ARG PRODUCT=hub
ARG VERSION
USER root


RUN <<c81b94933420221a7ac004a90242d8b1d3e5070d sh
apt-get update
apt-get install -y --no-install-recommends zip unzip
cd /opt/$PRODUCT/apps/hub/web/WEB-INF/lib
unzip jetbrains.jetpass.notification-$VERSION.jar templates/*
sed -i 's/ nicht / /' templates/de/email-notification-layout.ftl
sed -i 's/ do not / /' templates/en/email-notification-layout.ftl
zip -rf jetbrains.jetpass.notification-$VERSION.jar templates
rm -rf templates
cd /
apt-get purge -y --autoremove zip unzip
apt-get clean
rm -rf /var/lib/apt/lists/*
c81b94933420221a7ac004a90242d8b1d3e5070d


USER jetbrains
