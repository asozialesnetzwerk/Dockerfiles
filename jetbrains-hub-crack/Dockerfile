# syntax=docker.io/docker/dockerfile:1.4
ARG VERSION
ARG IMAGE=docker.io/jetbrains/hub:$VERSION
FROM $IMAGE
ARG PRODUCT=hub
ARG VERSION
USER root


RUN <<c81b94933420221a7ac004a90242d8b1d3e5070d sh
mkdir -p /usr/share/man/man1
apt-get update
apt-get install -y --no-install-recommends curl openjdk-11-jdk-headless patch zip
curl -ko cfr.jar https://www.benf.org/other/cfr/cfr-0.150.jar
cd /opt/$PRODUCT/apps/hub/web/WEB-INF/lib
java -cp .:/cfr.jar org.benf.cfr.reader.Main jetbrains.ring.license.reader.LicenseReader --outputdir .

<<f291ba5015df348c80853fa5bb0f7946f5c9e1b3 patch -p0
--- jetbrains/ring/license/reader/LicenseReader.java
+++ jetbrains/ring/license/reader/LicenseReader.java
@@ -51,7 +51,11 @@
         BitStream license = new BitStream(licenseNumber.modPow(LicenseUtil.THREE, this.rsaMOD));
         BigInteger licenseMD5 = license.readBigInteger(128);
         if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license.getStream()))) {
-            throw new InvalidLicenseSignatureException();
+            license = new BitStream(licenseNumber);
+            licenseMD5 = license.readBigInteger(128);
+            if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license.getStream()))) {
+                throw new InvalidLicenseSignatureException();
+            }
         }
         BigInteger usernameMD5 = license.readBigInteger(128);
         if (!usernameMD5.equals(LicenseUtil.getUsernameMD5(licenseName))) {
f291ba5015df348c80853fa5bb0f7946f5c9e1b3

sh -c "javac --release 8 -g -cp \$(ls annotations-*.jar):jetbrains.ring.license.checker-$VERSION.jar jetbrains/ring/license/reader/LicenseReader.java"
zip -rf jetbrains.ring.license.checker-$VERSION.jar jetbrains
rm -rf jetbrains
cd /
rm -f cfr.jar
apt-get purge -y --autoremove curl openjdk-11-jdk-headless patch zip
apt-get clean
rm -rf /var/lib/apt/lists/*
c81b94933420221a7ac004a90242d8b1d3e5070d


USER jetbrains
