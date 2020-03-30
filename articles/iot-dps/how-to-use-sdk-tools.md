---
title: Använda Azure IoT Hub Device Provisioning Service SDK:er-verktyg
description: Det här dokumentet granskar de verktyg som finns i DPS-SDK:er (Azure IoT Hub Device Provisioning Service) för utveckling
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271530"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Hur man använder verktyg som finns i SDK:erna för att förenkla utvecklingen för etablering
IoT Hub Device Provisioning Service förenklar etableringsprocessen med noll-touch, just-in-time [automatisk etablering](concepts-auto-provisioning.md) på ett säkert och skalbart sätt.  Säkerhetsintyg i form av X.509-certifikat eller TPM (Trusted Platform Module) krävs.  Microsoft samarbetar också med andra partner för [säkerhetsmaskinvara](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) för att öka förtroendet för att skydda IoT-distributionen. Att förstå maskinvarusäkerhetskravet kan vara ganska utmanande för utvecklare. En uppsättning Azure IoT-etableringstjänst-SDK:er tillhandahålls så att utvecklare kan använda ett bekvämlighetslager för att skriva klienter som pratar med etableringstjänsten. SDK:erna tillhandahåller också exempel på vanliga scenarier samt en uppsättning verktyg för att förenkla säkerhetsintyg under utveckling.

## <a name="trusted-platform-module-tpm-simulator"></a>TPM-simulator (Trusted Platform Module)
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) kan referera till en standard för säker lagring av nycklar för att autentisera plattformen, eller det kan hänvisa till I / O-gränssnittet som används för att interagera med modulerna som implementerar standarden. TPM kan finnas som diskret maskinvara, integrerad maskinvara, firmware-baserad eller programvarubaserad.  I produktion finns TPM på enheten, antingen som diskret maskinvara, integrerad maskinvara eller firmwarebaserad. I testfasen tillhandahålls en programvarubaserad TPM-simulator till utvecklare.  Den här simulatorn är endast tillgänglig för utveckling på Windows-plattformen för tillfället.

Steg för att använda TPM-simulatorn är:
1. [Förbered utvecklingsmiljön](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) och klona GitHub-databasen:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navigera till TPM-simulatormappen under ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Kör Simulator.exe innan du kör något klientprogram för etableringsenhet.
4. Låt simulatorn köras i bakgrunden under hela etableringsprocessen för att få registrerings-ID och bekräftelsenyckel.  Båda värdena är endast giltiga för en instans av körningen.

## <a name="x509-certificate-generator"></a>X.509 certifikat generator
[X.509-certifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) kan användas som en attestation mekanism för att skala produktionen och förenkla enhetsetablering.  Det finns [flera sätt](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) att få ett X.509-certifikat:
* För produktionsmiljö rekommenderar vi att du köper ett X.509 CA-certifikat från en offentlig rotcertifikatutfärdarmyndighet.
* För testmiljö kan du generera ett X.509-rotcertifikat eller X.509-certifikatkedja med:
    * OpenSSL: Du kan använda skript för certifikatgenerering:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell eller Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * DICE-emulator (Device Identity Composition Engine): DICE kan användas för kryptografisk enhetsidentitet och attestation baserat på TLS-protokoll och X.509-klientcertifikat.  [Läs](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mer om enhetens identitet med DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Använda X.509 certifikatgenerator med DICE-emulator
SDK:erna tillhandahåller en X.509-certifikatgenerator med DICE-emulatorn, som ligger i [Java SDK.](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)  Denna generator fungerar plattformsoberoende.  Det genererade certifikatet kan användas för utveckling på andra språk.

För närvarande, medan DICE Emulator matar ut ett rotcertifikat, ett mellanliggande certifikat, ett lövcertifikat och tillhörande privat nyckel.  Rotcertifikatet eller mellanliggande certifikatet kan dock inte användas för att signera ett separat lövcertifikat.  Om du tänker testa gruppregistreringsscenariot där ett signeringscertifikat används för att signera lövcertifikat för flera enheter kan du använda OpenSSL för att skapa en certifikatkedja.

Så här genererar du X.509-certifikat med den här generatorn:
1. [Förbered utvecklingsmiljön](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) och klona GitHub-databasen:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Ändra roten till azure-iot-sdk-java.
3. Kör ```mvn install -DskipTests=true``` för att hämta alla nödvändiga paket och kompilera SDK
4. Navigera till roten för X.509 Certificate Generator i ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Bygg med```mvn clean install```
6. Kör verktyget med följande kommandon:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Vid uppmaning kan du ange ett _eget namn_ för dina certifikat.
8. Verktyget genererar lokalt ett **klientcertifikat,** **den privata nyckeln client cert,** **Intermediate Cert**och **Root Cert**.

**Klientcertifikat** är lövcertifikatet på enheten.  **Client Cert** och den associerade **Client Cert Private Key** behövs i enhetsklienten. Beroende på vilket språk du väljer kan mekanismen för att placera detta i klientprogrammet vara annorlunda.  Mer information finns i [snabbstarterna](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) på skapa simulerad enhet med X.509 för mer information.

Rotcertifikatet eller mellanliggande kan användas för att skapa en registreringsgrupp eller individuell registrering [programmässigt](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) eller med hjälp av [portalen](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Nästa steg
* Utveckla med azure [IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub Device Provisioning Service
