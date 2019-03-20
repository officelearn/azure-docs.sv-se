---
title: Använd verktyg som finns i Azure IoT Hub Device Provisioning Service SDK för att förenkla utveckling
description: Det här dokumentet granskas av verktygen i Azure IoT Hub Device Provisioning Service SDK för utveckling
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123682"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Hur du använder verktyg som finns i SDK: erna för att förenkla utvecklingen för etablering
IoT Hub Device Provisioning Service förenklar etableringen med zero-touch-in-time [Automatisk etablering](concepts-auto-provisioning.md) på ett säkert och skalbart sätt.  Security-attestering i form av X.509-certifikat eller Trusted Platform Module (TPM) krävs.  Microsoft även i samarbete med [andra security maskinvarupartners](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) att förbättra förtroende för säkra IoT-distributionen. Förstå säkerhet maskinvarukrav kan vara ganska svårt för utvecklare. En uppsättning Azure IoT Provisioning Service-SDK: er tillhandahålls så att utvecklare kan använda ett bekvämlighet lager för skrivning klienter som kommunicerar med etableringstjänsten. SDK: erna kan du även ange exempel för vanliga scenarier som en uppsättning verktyg för att förenkla security attestering under utveckling.

## <a name="trusted-platform-module-tpm-simulator"></a>Betrodda Platform Module (TPM)-simulator
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) kan referera till en standard för att på ett säkert sätt lagra nycklar för att autentisera plattformen eller den kan referera till i/o-gränssnitt som används för att interagera med moduler som implementerar standarden. TPM: er kan finnas som diskreta maskinvara, integrerad maskinvara, inbyggd programvara eller programvara.  I produktion, TPM finns på enheten, antingen som diskreta maskinvara, integrerad maskinvara eller firmware-baserade. I testfasen tillhandahåller en programvarubaserad TPM-simulator för utvecklare.  Den här simulatorn är endast tillgänglig för att utveckla på Windows-plattformen för tillfället.

Steg för att använda TPM-simulatorn är:
1. [Förbereda utvecklingsmiljön](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) och klona GitHub-lagringsplatsen:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navigera till TPM-simulatormappen under ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Kör Simulator.exe innan du kör valfritt program för etablering av enheten.
4. Kan köras i bakgrunden under etableringsprocessen att hämta registrerings-ID och bekräftelsenyckeln simulatorn.  Båda värdena är bara giltiga för en instans av körningen.

## <a name="x509-certificate-generator"></a>X.509 certificate generator
[X.509-certifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) kan användas som en attesteringsmetod skala produktion och förenkla enhetsetablering.  Det finns [på flera olika sätt](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) att erhålla ett X.509-certifikat:
* För produktionsmiljö rekommenderar vi att köpa ett X.509 CA-certifikat från en offentlig rotcertifikatutfärdare.
* För att testa miljön, kan du generera ett X.509-rotcertifikat eller X.509-rotcertifikatutfärdarcertifikat kedja med:
    * OpenSSL: Du kan använda skript för skapande av certifikat:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell- eller Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Identity Composition Engine (DICE) enhetsemulatorn: DICE kan användas för kryptografiska enhetsidentitet och attestering baserat på TLS-protokollet och X.509 klientcertifikat.  [Lär dig](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mer om enhetsidentitet med DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Med hjälp av X.509 certificate generator med DICE-emulator
SDK: er ger ett X.509 certificate generator med DICE-emulator, som finns i den [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Den här generator fungerar olika plattformar.  Det genererade certifikatet kan användas för utveckling på andra språk.

När du för närvarande DICE-Emulator matar ut ett rotcertifikat, ett mellanliggande certifikat, en lövcertifikat och tillhörande privat nyckel.  Rotcertifikat- eller mellanliggande certifikat kan inte dock användas för att logga en separat lövcertifikatet.  Om du planerar att testa scenariot för registrering av grupp där ett signeringscertifikat används för att signera löv-certifikat för flera enheter, kan du använda OpenSSL för att skapa en kedja av certifikat.

Så här genererar X.509-certifikat med hjälp av den här generator:
1. [Förbereda utvecklingsmiljön](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) och klona GitHub-lagringsplatsen:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Ändra roten till azure-iot-sdk-java.
3. Kör ```mvn install -DskipTests=true``` att hämta alla nödvändiga paket och kompilera SDK
4. Navigera till roten för X.509 Certificate Generator i ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Skapa med ```mvn clean install```
6. Kör verktyget med följande kommandon:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Vid uppmaning kan du ange ett _eget namn_ för dina certifikat.
8. Verktyget genererar lokalt en **Client Cert**, **privata Klientcertifikatnyckel**, **mellanliggande certifikat**, och **Root Cert**.

**Klientcertifikatet** är lövcertifikatet på enheten.  **Klientcertifikatet** och den associerade **privata Klientcertifikatnyckel** behövs i enhetsklienten. Mekanism för att placera det i klientprogrammet kan skilja sig beroende på vilket språk som du väljer.  Mer information finns i den [Snabbstarter](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) vid skapande simulerad enhet med X.509 för mer information.

Rotcertifikat eller mellanliggande certifikat kan användas för att skapa en grupp för registrering eller en enskild registrering [programmässigt](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) eller med hjälp av den [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Nästa steg
* Utveckla med hjälp av den [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub Device Provisioning-tjänsten
