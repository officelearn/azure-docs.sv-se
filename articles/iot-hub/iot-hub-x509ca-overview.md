---
title: Översikt över Azure IoT Hub X.509 CA-säkerhet | Microsoft-dokument
description: Översikt - hur du autentiserar enheter till IoT Hub med X.509 Certifikatutfärdare.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284517"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Enhetsautentisering med X.509-certifikatutfärdarcertifikat

I den här artikeln beskrivs hur du använder Certifikatutfärdarecertifikat för X.509 för att autentisera enheter som ansluter IoT Hub.  I den här artikeln får du lära dig:

* Så här skaffar du ett X.509 CA-certifikat
* Så här registrerar du X.509 CA-certifikatet till IoT Hub
* Så här signerar du enheter med X.509 CA-certifikat
* Så här autentiseras enheter som är signerade med X.509 CA

## <a name="overview"></a>Översikt

Funktionen X.509 CA möjliggör enhetsautentisering till IoT Hub med hjälp av en certifikatutfärdare . Det förenklar avsevärt den inledande enhetsregistreringsprocessen och logistik i leveranskedjan under enhetstillverkning. [Läs mer i den här scenarioartikeln om värdet av att använda X.509 CA-certifikat](iot-hub-x509ca-concept.md) för enhetsautentisering.  Vi rekommenderar att du läser den här scenarioartikeln innan du fortsätter eftersom det förklarar varför stegen som följer finns.

## <a name="prerequisite"></a>Krav

Med hjälp av X.509 CA-funktionen krävs att du har ett IoT Hub-konto.  [Läs om hur du skapar en IoT Hub-instans](quickstart-send-telemetry-dotnet.md) om du inte redan har en.

## <a name="how-to-get-an-x509-ca-certificate"></a>Så här skaffar du ett X.509 CA-certifikat

X.509 CA-certifikatet finns högst upp i certifikatkedjan för var och en av dina enheter.  Du kan köpa eller skapa en beroende på hur du tänker använda den.

För produktionsmiljön rekommenderar vi att du köper ett X.509 CA-certifikat från en offentlig rotcertifikatutfärdarmyndighet. Att köpa ett CA-certifikat har fördelen att rotcertifikatutfärdaren fungerar som en betrodd tredje part för att gå i god för dina enheters legitimitet. Tänk på det här alternativet om du vill att dina enheter ska ingå i ett öppet IoT-nätverk där de förväntas interagera med produkter eller tjänster från tredje part.

Du kan också skapa en självsignerad X.509 CA för experiment eller för användning i slutna IoT-nätverk.

Oavsett hur du skaffar ditt X.509 CA-certifikat, se till att hålla motsvarande privata nyckel hemlig och skyddad hela tiden.  Detta är nödvändigt för förtroendeskapande förtroende för X.509 CA-autentisering.

Lär dig hur du [skapar ett självsignerat CERTIFIKATUTfärdarcertifikat](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)som du kan använda för experiment under hela den här funktionsbeskrivningen.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Logga in enheter i certifikatkedjan för förtroende

Ägaren till ett X.509 CA-certifikat kan kryptografiskt signera en mellanliggande certifikatutfärdare som i sin tur kan signera en annan mellanliggande certifikatutfärdare, och så vidare, tills den sista mellanliggande certifikatutfärdaren avslutar den här processen genom att signera en enhet. Resultatet är en kaskad kedja av certifikat som kallas en certifikatkedja för förtroende. I verkliga livet detta spelar ut som delegering av förtroende för signering enheter. Den här delegeringen är viktig eftersom den upprättar en kryptografiskt variabel kedja av vårdnad och undviker att dela signeringsnycklar.

![img-generisk-cert-kedja-av-förtroende](./media/generic-cert-chain-of-trust.png)

Enhetscertifikatet (kallas även lövcertifikat) måste ha *ämnesnamnet* inställt på **enhets-ID** som användes när IoT-enheten registrerades i Azure IoT Hub. Den här inställningen krävs för autentisering.

Läs om hur du [skapar en certifikatkedja](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) som du gör när du signerar enheter.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Så här registrerar du X.509 CA-certifikatet till IoT Hub

Registrera ditt X.509 CA-certifikat till IoT Hub där det används för att autentisera dina enheter under registrering och anslutning.  Registrering av X.509 CA-certifikatet är en tvåstegsprocess som omfattar uppladdning av certifikatfiler och bevis på innehav.

Uppladdningsprocessen innebär att du överför en fil som innehåller ditt certifikat.  Den här filen får aldrig innehålla några privata nycklar.

Beviset på innehav steg innebär en kryptografisk utmaning och svarsprocess mellan dig och IoT Hub.  Med tanke på att innehållet i digitala certifikat är offentligt och därför kan avlyssnas, vill IoT Hub försäkra dig om att du verkligen äger CERTIFIKATUTfärdarcertifikatet.  Den ska göra detta genom att generera en slumpmässig utmaning som du måste signera med ca-certifikatets motsvarande privata nyckel.  Om du höll den privata nyckeln hemlig och skyddad som tidigare rekommenderas, då bara du kommer att ha kunskap för att slutföra detta steg. Sekretess för privata nycklar är källan till förtroende för denna metod.  När du har signerats utmaningen slutför du det här steget genom att ladda upp en fil som innehåller resultaten.

Läs mer om hur du [registrerar ditt CA-certifikat](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Så här skapar du en enhet på IoT Hub

För att förhindra personifiering av enheter kräver IoT Hub att du meddelar den vilka enheter som väntar.  Du gör detta genom att skapa en enhetspost i IoT Hub enhetsregister.  Den här processen automatiseras när du använder IoT Hub [Device Provisioning Service](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Läs om hur du [manuellt skapar en enhet i IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Skapa en X.509-enhet för din IoT-hubb

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autentisera enheter som är signerade med X.509 CA-certifikat

Med X.509 CA-certifikat registrerat och enheter signerade i en certifikatkedja av förtroende, vad som återstår är enhetsautentisering när enheten ansluter, även för första gången.  När en X.509 CA-signerad enhet ansluter överförs certifikatkedjan för validering. Kedjan innehåller alla mellanliggande certifikatutfärdare och enhetscertifikat.  Med den här informationen autentiserar IoT Hub enheten i en tvåstegsprocess.  IoT Hub validerar kryptografiskt certifikatkedjan för intern konsekvens och utfärdar sedan en proof-of-possession-utmaning till enheten.  IoT Hub förklarar enheten autentisk på ett lyckat bevis på innehav svar från enheten.  Den här deklarationen förutsätter att enhetens privata nyckel är skyddad och att endast enheten kan svara på den här utmaningen.  Vi rekommenderar användning av säkra chips som Hardware Secure Modules (HSM) i enheter för att skydda privata nycklar.

En lyckad enhetsanslutning till IoT Hub slutför autentiseringsprocessen och är också ett tecken på en korrekt installation.

Läs om hur du [slutför det här enhetsanslutningssteget](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Efterföljande moment

Lär dig mer om [värdet för X.509 CA-autentisering](iot-hub-x509ca-concept.md) i IoT.

Komma igång med IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/).
