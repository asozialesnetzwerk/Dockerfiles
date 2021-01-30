ARG HUB_VERSION=2020.1.12722
ARG IMAGE=jetbrains/hub:$HUB_VERSION
FROM $IMAGE
ARG PRODUCT=hub
ARG HUB_VERSION
USER root
RUN mkdir -p /usr/share/man/man1 \
 && echo "deb http://deb.debian.org/debian stretch-backports main" > /etc/apt/sources.list.d/backports.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends ca-certificates curl patch zip openjdk-11-jdk-headless \
 && cd /opt/$PRODUCT/apps/hub/web/WEB-INF/lib \
 && curl https://www.benf.org/other/cfr/cfr-0.150.jar -o cfr.jar \
 && java -cp cfr.jar:jetbrains.ring.license.checker-$HUB_VERSION.jar org.benf.cfr.reader.Main jetbrains.ring.license.reader.LicenseReader --outputdir . \
 && printf "%s\n" \
    '--- jetbrains/ring/license/reader/LicenseReader.java' \
    '+++ jetbrains/ring/license/reader/LicenseReader.java' \
    '@@ -53,7 +53,11 @@' \
    '         BitStream license = new BitStream(licenseNumber.modPow(LicenseUtil.THREE, this.rsaMOD));' \
    '         BigInteger licenseMD5 = license.readBigInteger(128);' \
    '         if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license.getStream()))) {' \
    '-            throw new InvalidLicenseSignatureException();' \
    '+            license = new BitStream(licenseNumber);' \
    '+            licenseMD5 = license.readBigInteger(128);' \
    '+            if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license.getStream()))) {' \
    '+                throw new InvalidLicenseSignatureException();' \
    '+            }' \
    '         }' \
    '         BigInteger usernameMD5 = license.readBigInteger(128);' \
    '         if (!usernameMD5.equals(LicenseUtil.getUsernameMD5(licenseName))) {' \
  | patch -p0 \
 && javac --release 8 -g -cp annotations-15.0.jar:jetbrains.ring.license.checker-$HUB_VERSION.jar jetbrains/ring/license/reader/LicenseReader.java \
 && zip -rf jetbrains.ring.license.checker-$HUB_VERSION.jar jetbrains \
 && rm -rf jetbrains \
 && rm -f cfr.jar \
 && apt-get purge -y --autoremove ca-certificates curl patch zip openjdk-11-jdk-headless \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && rm -f /etc/apt/sources.list.d/backports.list
USER jetbrains
