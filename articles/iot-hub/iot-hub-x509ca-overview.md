---
title: Översikt över Azure IoT Hub X.509 CA-säkerhet | Microsoft Docs
description: Översikt – så här att autentisera enheter till IoT Hub med X.509-certifikatutfärdare.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 6a9b4fc5479dda58dd024cdf93cbdf4853f9c965
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42059591"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autentisering med X.509 CA-certifikat

Den här artikeln beskriver hur du använder X.509 certifikatutfärdaren (CA)-certifikat för att autentisera enheter som ansluter IoT Hub.  I den här artikeln lär du:

* Så här hämtar du ett X.509 CA-certifikat
* Hur du registrerar X.509 CA-certifikat till IoT Hub
* Hur du registrerar enheter med X.509 CA-certifikat
* Hur enheter som har signerats med X.509 CA autentiseras

## <a name="overview"></a>Översikt

Med X.509 CA-funktionen kan autentisering till IoT Hub med hjälp av en certifikatutfärdare (CA). Det förenklar avsevärt starttillstånd registreringsprocessen och strömförsörjning kedja logistik under enhetens tillverkning. [Läs mer i det här scenariot artikel om värdet för att använda X.509 CA-certifikat](iot-hub-x509ca-concept.md) för autentisering.  Vi rekommenderar att du kan läsa det här scenariot artikeln innan du fortsätter enligt det förklarar varför de steg som följer finns.

## <a name="prerequisite"></a>Krav

Med X.509 CA-funktionen kräver att du har en IoT Hub-konto.  [Lär dig hur du skapar en IoT Hub-instansen](quickstart-send-telemetry-dotnet.md) om du inte redan har ett.

## <a name="how-to-get-an-x509-ca-certificate"></a>Så här hämtar du ett X.509 CA-certifikat

X.509 CA-certifikatet är högst upp på kedja av certifikat för var och en av dina enheter.  Du kan köpa eller skapa ett beroende på hur du planerar att använda den.

För produktionsmiljö rekommenderar vi att du köper ett X.509 CA-certifikat från en offentlig rotcertifikatutfärdare. Köpa ett CA-certifikat har fördelen med rotcertifikatutfärdaren fungerar som en betrodd tredje part till garanterar giltighet dina enheter. Överväg det här alternativet om du planerar din enheter måste vara en del av ett öppet IoT-nätverk där de förväntas interagera med från tredje part, produkter eller tjänster.

Du kan också skapa ett självsignerat X.509 CA för experimentering eller för användning i stängda IoT-nätverk.

Oavsett hur du skaffa X.509 CA-certifikat, se till att välja dess motsvarande privata nyckeln hemlig och skyddas tiden hela.  Detta är nödvändigt för förtroende för att skapa förtroendet i X.509 CA-autentisering. 

Lär dig hur du [skapa ett självsignerat certifikat](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), som du kan använda för experimentering i hela den här funktionen beskrivningen.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Registrera enheter i certifikatkedja med förtroenden

Ägaren av ett X.509 CA-certifikat kan kryptografiskt logga en mellanliggande Certifikatutfärdare som i sin tur kan registrera en annan mellanliggande Certifikatutfärdare och så vidare, fram till den senaste mellanliggande CA avslutar den här processen genom att registrera en enhet. Resultatet är en sammanhängande kedja av certifikat som kallas en certifikatkedja med förtroenden. I verkligheten spelar det som delegering av förtroende för att registrera enheter. Den här delegeringen är viktigt eftersom den upprättar en kryptografiskt variabeln spårbarhet och på så sätt undviker delning av Signeringsnycklar.

![img-Generic-CERT-chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Läs här så [en certifikatkedja](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) som görs när du registrerar enheter.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Hur du registrerar X.509 CA-certifikat till IoT Hub

Registrera ditt X.509 CA-certifikat till IoT Hub där den används för att autentisera dina enheter under registreringen och anslutning.  Registrera X.509 CA-certifikatet är en tvåstegsprocess som består av certifikat filuppladdning och bevis på tillgång.

Ladda upp signeras ladda upp en fil som innehåller ditt certifikat.  Den här filen innehåller bör aldrig privata nycklar.

Proof of tillgång steg innebär en kryptografisk utmaning och processen för svar mellan dig och IoT Hub.  Med hänsyn till att digitalt certifikat innehållet är offentliga och därför känslig för avlyssning, vill IoT Hub säkerställa att du verkligen äger CA-certifikatet.  Det bör göra det genom att generera en slumpmässig utmaning som måste du logga med CA-certifikatet motsvarande privata nyckel.  Om du behöll den privata nyckeln hemliga och skyddade som tidigare bäst, och du kommer endast har kunskapen för att slutföra det här steget. Hemlig privata nycklar är källan för förtroende för den här metoden.  Slutför det här steget när du har registrerat utmaningen genom att ladda upp en fil som innehåller resultatet.

Läs här så [registrera Certifikatutfärdarens certifikat](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Så här skapar du en enhet på IoT-hubb

Om du vill hindra enheten personifiering, måste IoT Hub du så att den vet vilka enheter du kan förvänta dig.  Du kan göra detta genom att skapa en post i enhetsregistret för IoT-hubben.  Den här processen sker automatiskt när du använder IoT Hub [Device Provisioning-tjänsten](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Läs här så [manuellt skapa en enhet i IoT Hub](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autentisera enheter signeras med X.509 CA-certifikat

Vad som finns kvar är autentisering från med X.509 CA-certifikat som är registrerat och enheter som har loggat in på en certifikatkedja med förtroenden när enheten ansluter även för första gången.  När ett X.509 CA-signerat enheten ansluter, laddar de upp sina certifikatkedja för verifiering. Kedjan innehåller alla mellanliggande CA- och enhetscertifikat.  Med den här informationen autentiserar IoT Hub enheten i två steg.  IoT Hub verifierar kryptografiskt certifikatkedja för intern inkonsekvens och skickar sedan en utmaning bevis för tillgång till enheten.  IoT Hub deklarerar enheten autentisk på ett lyckat bevis på tillgång svar från enheten.  Deklarationen förutsätter att enhetens privata nyckeln skyddas och att endast enheten har kan svara på den här utmaningen.  Vi rekommenderar användning av säker kretsar som maskinvara säker moduler (HSM) i enheter för att skydda privata nycklar.

En lyckad enhetsanslutning till IoT Hub Slutför autentiseringen och är också en indikation på en korrekt installation.

Läs här så [slutföra det här steget för anslutning av enheten](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [värdet för autentisering med X.509 CA](iot-hub-x509ca-concept.md) i IoT.

Kom igång med IoT Hub [Device Provisioning-tjänsten](https://docs.microsoft.com/azure/iot-dps/).
