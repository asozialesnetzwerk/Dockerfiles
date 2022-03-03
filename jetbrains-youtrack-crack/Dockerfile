ARG VERSION
ARG IMAGE=docker.io/jetbrains/youtrack:$VERSION
FROM $IMAGE
ARG VERSION
USER root
RUN mkdir -p /usr/share/man/man1 \
 && apt-get update \
 && apt-get install -y --no-install-recommends curl patch zip openjdk-11-jdk-headless \
 && curl -ko cfr.jar https://www.benf.org/other/cfr/cfr-0.150.jar \
 && cd /opt/youtrack/apps/youtrack/web/WEB-INF/lib \
 && java -cp .:/cfr.jar org.benf.cfr.reader.Main jetbrains.teamsys.license.runtime.LicenseChecker --outputdir . \
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
 && javac --release 8 -g -cp $(ls annotations-*.jar):youtrack-webr-$VERSION.jar jetbrains/teamsys/license/runtime/LicenseChecker.java \
 && zip -rf youtrack-webr-$VERSION.jar jetbrains \
 && rm -rf jetbrains \
 && cd /opt/youtrack/internal/wizard_web/WEB-INF/lib \
 && java -cp .:/cfr.jar org.benf.cfr.reader.Main jetbrains.ring.license.reader.LicenseReader --outputdir . \
 && printf "%s\n" \
        '--- jetbrains/ring/license/reader/LicenseReader.java' \
        '+++ jetbrains/ring/license/reader/LicenseReader.java' \
        '@@ -49,7 +49,11 @@' \
        '         BitStream license = new BitStream(licenseNumber.modPow(THREE, RSA_MOD));' \
        '         BigInteger licenseMD5 = license.readBigInteger(128);' \
        '         if (!licenseMD5.equals(LicenseReader.getLicenseMD5(license.getStream()))) {' \
        '-            throw new InvalidLicenseSignatureException();' \
        '+            license = new BitStream(licenseNumber);' \
        '+            licenseMD5 = license.readBigInteger(128);' \
        '+            if (!licenseMD5.equals(LicenseReader.getLicenseMD5(license.getStream()))) {' \
        '+                throw new InvalidLicenseSignatureException();' \
        '+            }' \
        '         }' \
        '         BigInteger usernameMD5 = license.readBigInteger(128);' \
        '         if (!usernameMD5.equals(LicenseReader.getUsernameMD5(licenseName))) {' \
  | patch -p0 \
 && javac --release 8 -g -cp $(ls annotations-*.jar):ring-license-checker-1.0.32.jar jetbrains/ring/license/reader/LicenseReader.java \
 && zip -rf ring-license-checker-1.0.32.jar jetbrains \
 && rm -rf jetbrains \
 && cd / \
 && rm -f cfr.jar \
 && apt-get purge -y --autoremove curl patch zip openjdk-11-jdk-headless \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*
USER jetbrains
