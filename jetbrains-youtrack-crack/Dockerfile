ARG VERSION
ARG IMAGE=jetbrains/youtrack:$VERSION
FROM $IMAGE
ARG VERSION
USER root
RUN mkdir -p /usr/share/man/man1 \
 && echo "deb http://deb.debian.org/debian stretch-backports main" > /etc/apt/sources.list.d/backports.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends ca-certificates curl patch zip openjdk-11-jdk-headless \
 && cd /opt/youtrack/apps/youtrack/web/WEB-INF/lib \
 && curl https://www.benf.org/other/cfr/cfr-0.150.jar -o cfr.jar \
 && java -cp cfr.jar:youtrack-webr-$VERSION.jar org.benf.cfr.reader.Main jetbrains.teamsys.license.runtime.LicenseChecker --outputdir . \
 && printf "%s\n" \
    '--- jetbrains/teamsys/license/runtime/LicenseChecker.java' \
    '+++ jetbrains/teamsys/license/runtime/LicenseChecker.java' \
    '@@ -19,13 +19,16 @@' \
    '     private final int licenseType;' \
    ' ' \
    '     public LicenseChecker(String name, BigInteger license, BigInteger n) throws GeneralSecurityException {' \
    '-        license = license.modPow(LicenseUtil.THREE, n);' \
    '         BigInteger maskOfMD5 = LicenseUtil.ONE.shiftLeft(128).subtract(LicenseUtil.ONE);' \
    '         BigInteger licenseMD5 = license.and(maskOfMD5);' \
    '-        license = license.shiftRight(128);' \
    '-        if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license))) {' \
    '-            throw new InvalidLicenseException("License is invalid");' \
    '+        if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license.shiftRight(128)))) {' \
    '+            license = license.modPow(LicenseUtil.THREE, n);' \
    '+            licenseMD5 = license.and(maskOfMD5);' \
    '+            if (!licenseMD5.equals(LicenseUtil.getLicenseMD5(license.shiftRight(128)))) {' \
    '+                throw new InvalidLicenseException("License is invalid");' \
    '+            }' \
    '         }' \
    '+        license = license.shiftRight(128);' \
    '         BigInteger usernameMD5 = license.and(maskOfMD5);' \
    '         if (!usernameMD5.equals(LicenseUtil.getUsernameMD5(name))) {' \
    '             throw new InvalidLicenseException("License name is invalid", true);' \
  | patch -p0 \
 && javac --release 8 -g -cp annotations-19.0.jar:youtrack-webr-$VERSION.jar jetbrains/teamsys/license/runtime/LicenseChecker.java \
 && zip -rf youtrack-webr-$VERSION.jar jetbrains \
 && rm -rf jetbrains \
 && rm -f cfr.jar \
 && apt-get purge -y --autoremove ca-certificates curl patch zip openjdk-11-jdk-headless \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && rm -f /etc/apt/sources.list.d/backports.list
USER jetbrains
