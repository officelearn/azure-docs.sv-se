---
title: Förenkla utvecklingen med hjälp av verktygen i Azure IoT Hub-enhet etablering Service SDK
description: 'Det här dokumentet går igenom de verktyg som finns i Azure IoT Hub-enhet etablering Service SDK: er för utveckling'
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: afc7059f3b066ac5f3c9b49d543bc2b3e52ad6af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631130"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Hur du använder verktygen i SDK: erna för att förenkla utvecklingen för etablering
IoT-hubb enheten Etableringstjänsten förenklar etableringsprocessen med zero touch-in-time [Automatisk etablering](concepts-auto-provisioning.md) på en säker och skalbar sätt.  Attesteringen av säkerhet i form av X.509-certifikat eller Trusted Platform Module (TPM) måste anges.  Dessutom samarbetar Microsoft med [andra säkerhet maskinvarupartners](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) att förbättra förtroendet säkra IoT-distributionen. Förstå maskinvarukrav för säkerhet kan vara ganska svårt för utvecklare. En uppsättning Azure IoT etablering Service SDK tillhandahålls så att utvecklare kan använda ett bekvämlighet lager för skrivning klienter som kommunicerar med tjänsten etablering. SDK: erna dessutom exempel för vanliga scenarier som en uppsättning verktyg för att förenkla säkerhet attestering under utveckling.

## <a name="trusted-platform-module-tpm-simulator"></a>Betrodda Platform Module (TPM) simulator
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) kan referera till en standard för säker lagring av nycklar för att autentisera plattformen eller den kan referera till i/o-gränssnitt som används för att interagera med moduler som implementerar standarden. TPM: er kan finnas som diskreta maskinvara, inbyggd maskinvara, inbyggd programvara eller programvara.  I produktion, TPM finns på enheten, antingen som diskreta maskinvara, inbyggd maskinvara eller inbyggd programvara. I testfasen tillhandahålls en programvarubaserad TPM-simulatorn för utvecklare.  Den här simulator är endast tillgängligt för att utveckla på Windows-plattformen för tillfället.

Steg för att använda TPM-simulatorn är:
1. [Förbereda utvecklingsmiljön](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) och klona GitHub-lagringsplatsen:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Navigera till mappen TPM simulator under ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Kör Simulator.exe innan du kör alla klientprogram för etablering enhet.
4. Kan köras i bakgrunden under etableringsprocessen att få registrerings-ID och bekräftelsenyckel simulatorn.  Båda värdena är bara giltig för en instans av körningen.

## <a name="x509-certificate-generator"></a>X.509-certifikat generator
[X.509-certifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) kan användas som en mekanism för attestering att skala produktion och förenkla enhetsetableringen.  Det finns [på flera olika sätt](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) att erhålla ett X.509-certifikat:
* För produktionsmiljö rekommenderar vi att köpa ett X.509-CA-certifikat från en offentlig rotcertifikatutfärdare.
* Du kan generera ett X.509-rotcertifikat eller kedja med för X.509-certifikat för att testa miljön:
    * OpenSSL: detta [så hjälper](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) går igenom exempel PowerShell-skript som använder [OpenSSL](https://www.openssl.org/) att skapa och signera X.509-certifikat.  Du kan dessutom också använda skriptet på andra språk för generering av certifikat:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Enhetens identitet sammansättning motorn (RAPPORTANVÄNDARNA)-Emulator: RAPPORTANVÄNDARNA kan användas för kryptografiska enhetsidentitet och attestering baserat på TLS-protokollet och X.509 klientcertifikat.  [Läs](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mer om enhetsidentitet med RAPPORTANVÄNDARNA.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Med RAPPORTANVÄNDARNA emulatorn generatorn för X.509-certifikat
SDK: erna ger ett X.509-certifikat generator med RAPPORTANVÄNDARNA emulator, finns i den [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Den här generator fungerar plattformar.  Det skapade certifikatet kan användas för utveckling på andra språk.

När du för närvarande RAPPORTANVÄNDARNA emulatorn matar ut ett rotcertifikat, ett mellanliggande certifikat, en lövcertifikatet och tillhörande privat nyckel.  Rotcertifikat och mellanliggande certifikat kan inte dock användas för att logga in en separat lövcertifikatet.  Om du vill testa grupp registreringsscenario där ett signeringscertifikat används för att signera certifikat löv av flera enheter, kan du använda OpenSSL för att skapa en kedja av certifikat.

Att generera X.509-certifikatet med den här generator:
1. [Förbereda utvecklingsmiljön](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) och klona GitHub-lagringsplatsen:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Ändra roten till azure iot-sdk-java.
3. Kör ```mvn install -DskipTests=true``` att hämta alla nödvändiga paketen och kompilera SDK
4. Navigera till roten för X.509-certifikat Generator i ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Skapa med ```mvn clean install```
6. Kör verktyget med följande kommandon:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Vid uppmaning kan du ange ett _eget namn_ för dina certifikat.
8. Verktyget lokalt genererar en **klienten Cert**, **klienten certifikatets privata nyckel**, **mellanliggande certifikat**, och **rot Cert**.

**Klienten Cert** är lövcertifikatet på enheten.  **Klienten Cert** och associerade **klienten certifikatets privata nyckel** behövs i klientenheter. En mekanism för att placera det i klientprogrammet kan vara olika beroende på vilket språk som du väljer.  Mer information finns i [Snabbstart](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) på Skapa simulerade enhet med X.509 för mer information.

Rotcertifikat och mellanliggande kan användas för att skapa en grupp för registrering eller enskilda registrering [programmässigt](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) eller med hjälp av den [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Nästa steg
* Utveckla med hjälp av den [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub etablering av tjänst