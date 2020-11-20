---
title: Använd Azure IoT Hub Device Provisioning Service SDK-verktyg
description: 'Det här dokumentet granskar de verktyg som finns i Azure IoT Hub Device Provisioning Service (DPS) SDK: er för utveckling'
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 984d38752df93f233c6d87458e3c9ba713696177
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967252"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Hur du använder verktyg som finns i SDK: er för att förenkla utvecklingen av etableringen
IoT Hub Device Provisioning Service fören klar etablerings processen med Zero Touch och just-in-Time- [etablering](about-iot-dps.md#provisioning-process) på ett säkert och skalbart sätt.  Säkerhets attestering i form av X. 509-certifikat eller Trusted Platform Module (TPM) krävs.  Microsoft samarbetar även med [andra säkerhets maskin varu partner](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) för att förbättra säkerheten vid IoT-distribution. Att förstå kraven på maskin varu säkerhet kan vara mycket utmanande för utvecklare. En uppsättning SDK: er för Azure IoT Provisioning-tjänsten tillhandahålls så att utvecklare kan använda ett bekvämlighets lager för att skriva klienter som kommunicerar med etablerings tjänsten. SDK: er innehåller också exempel på vanliga scenarier och en uppsättning verktyg för att förenkla säkerheten i utvecklingen.

## <a name="trusted-platform-module-tpm-simulator"></a>Trusted Platform Module (TPM) Simulator
[TPM](./concepts-service.md#attestation-mechanism) kan referera till en standard för säker lagring av nycklar för att autentisera plattformen, eller så kan den referera till i/O-gränssnittet som används för att interagera med modulerna som implementerar standarden. TPM: er kan finnas som diskret maskin vara, integrerad maskin vara, inbyggd program vara eller programvarubaserad.  I produktion finns TPM på enheten, antingen som diskret maskin vara, integrerad maskin vara eller inbyggd program vara. I test fasen tillhandahålls en programvarubaserad TPM-Simulator till utvecklare.  Den här simulatorn är bara tillgänglig för utveckling på Windows-plattformen för tillfället.

Steg för att använda TPM-simulatorn är:
1. [Förbered utvecklings miljön](./quick-enroll-device-x509-java.md) och klona GitHub-lagringsplatsen:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navigera till mappen TPM-simulator under ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` .
3. Kör Simulator.exe innan du kör något klient program för etablering av enheter.
4. Låt simulatorn köras i bakgrunden under etablerings processen för att hämta registrerings-ID och bekräftelse nyckel.  Båda värdena är bara giltiga för en instans av körningen.

## <a name="x509-certificate-generator"></a>Generator för X. 509-certifikat
[X. 509-certifikat](./concepts-x509-attestation.md#x509-certificates) kan användas som en mekanism för attestering för att skala produktion och förenkla enhets etablering.  Det finns [flera sätt](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) att hämta ett X. 509-certifikat:
* För produktions miljön rekommenderar vi att du köper ett X. 509 CA-certifikat från en offentlig rot certifikat utfärdare.
* I test miljö kan du generera ett X. 509-rot certifikat eller en X. 509-certifikat kedja med:
    * OpenSSL: du kan använda skript för att skapa certifikat:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell eller bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulator för enhets identitets sammansättnings motor (tärning): TÄRNINGar kan användas för kryptografisk enhets identitet och attestering baserat på TLS-protokoll och X. 509-klient certifikat.  [Lär dig](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mer om enhets identitet med tärningar.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Använda X. 509-certifikat Generator med tärning-emulator
SDK: erna tillhandahåller en 509 för X.-certifikat med tärning-emulatorn, som finns i [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Den här generatorn fungerar plattforms oberoende.  Det genererade certifikatet kan användas för utveckling på andra språk.

För närvarande, medan tärning-emulatorn matar ut ett rot certifikat, ett mellanliggande certifikat, ett löv certifikat och tillhör ande privat nyckel.  Rot certifikatet eller mellanliggande certifikat kan dock inte användas för att signera ett separat löv certifikat.  Om du vill testa ett registrerings scenario där ett signerings certifikat används för att signera löv certifikaten för flera enheter, kan du använda OpenSSL för att skapa en kedja av certifikat.

Skapa X. 509-certifikat med den här generatorn:
1. [Förbered utvecklings miljön](./quick-enroll-device-x509-java.md) och klona GitHub-lagringsplatsen:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Ändra roten till Azure-IoT-SDK-Java.
3. Kör ```mvn install -DskipTests=true``` för att hämta alla nödvändiga paket och kompilera SDK: n
4. Navigera till rot for X. 509 Certificate Generator i ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` .
5. Bygg med ```mvn clean install```
6. Kör verktyget med följande kommandon:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Vid uppmaning kan du ange ett _eget namn_ för dina certifikat.
8. Verktyget skapar ett **klient certifikat** lokalt, **klient certifikatets privata nyckel**, **mellanliggande certifikat** och **rot certifikatet**.

**Klient certifikat** är löv certifikatet på enheten.  **Klient certifikat** och associerad **privat nyckel för klient certifikat** krävs i enhets klienten. Beroende på vilket språk du väljer kan mekanismen för att ställa in detta i klient programmet vara annorlunda.  Mer information finns i [snabb starter](./quick-create-simulated-device-x509.md) på Skapa simulerad enhet med X. 509 för mer information.

Rot certifikatet eller mellanliggande kan användas för att skapa en registrerings grupp eller en enskild registrering via [programmering](./how-to-manage-enrollments-sdks.md) eller via [portalen](./how-to-manage-enrollments.md).

## <a name="next-steps"></a>Nästa steg
* Utveckla med [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub Device Provisioning service