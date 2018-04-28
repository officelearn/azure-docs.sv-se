---
title: Översikt över Azure IoT-hubb X.509 CA-säkerheten | Microsoft Docs
description: Översikt – så att autentisera enheter till IoT-hubb med X.509-certifikatutfärdare.
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 11f340ebe1719c680cce5f6ad34150d3717c57d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autentisering med X.509-certifikat

Den här artikeln beskriver hur du använder certifikat för X.509 certifikatutfärdare (CA) för att autentisera enheter som ansluter IoT-hubb.  I den här artikeln du lära dig:

* Hur du hämtar ett X.509-CA-certifikat
* Så här registrerar du X.509 CA-certifikatet till IoT-hubb
* Hur du registrerar enheter med hjälp av X.509-certifikat
* Hur enheter som har signerats med X.509 CA autentiseras

## <a name="overview"></a>Översikt

X.509-CA-funktionen kan enhetsautentisering till IoT-hubb med hjälp av en certifikatutfärdare (CA). Det förenklar kraftigt inledande enheten registreringen och ange kedjan logistik under enhetens tillverkning. [Läs mer i det här scenariot artikel om värdet med X.509-certifikat](iot-hub-x509ca-concept.md) för autentisering.  Vi rekommenderar att du till den här artikeln scenariot innan du fortsätter enligt det förklarar varför de steg som följer finns.

## <a name="prerequisite"></a>Krav

Med hjälp av funktionen X.509 Certifikatutfärdare kräver att du har ett konto för IoT-hubb.  [Lär dig hur du skapar en IoT-hubb instans](iot-hub-csharp-csharp-getstarted.md) om du inte redan har ett.

## <a name="how-to-get-an-x509-ca-certificate"></a>Hur du hämtar ett X.509-CA-certifikat

X.509-CA-certifikatet är högst upp i en kedja av certifikat för var och en av dina enheter.  Du kan köpa eller skapa ett beroende på hur du planerar att använda den.

För produktionsmiljö rekommenderar vi att du köper ett X.509-CA-certifikat från en offentlig rotcertifikatutfärdare. Köpa ett CA-certifikat har fördelen att rot-CA fungerar som en betrodd tredje part till garanterar om enheterna är giltiga. Överväg att det här alternativet om du vill att enheterna ska ingå i ett öppet IoT-nätverk där de förväntas interagera med tredje parts produkter eller tjänster.

Du kan också skapa ett självsignerat X.509-Certifikatutfärdare för undersökningar eller i stängd IoT-nätverk.

Oavsett hur du hämtar X.509 Certifikatutfärdarens certifikat, se till att hålla dess motsvarande privata nyckeln hemlig och skyddade när som helst.  Detta är nödvändigt för förtroende skapa förtroende i X.509 CA-autentisering. 

Lär dig hur du [skapa ett självsignerat certifikatutfärdarcertifikat](iot-hub-security-x509-create-certificates.md#createcerts), som du kan använda för experiment i hela den här funktionen beskrivningen.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Logga enheter i certifikatkedja med förtroenden

Ägaren av ett X.509-CA-certifikat kan kryptografiskt logga en mellanliggande Certifikatutfärdare som i sin tur kan signera en annan mellanliggande Certifikatutfärdare och så vidare tills de senaste mellanliggande Certifikatutfärdare avslutar processen genom att registrera en enhet. Resultatet är en sammanhängande kedja av certifikat som kallas en certifikatkedja med förtroenden. I verkligheten spelar det som delegering av förtroende för signering av enheter. Den här delegeringen är viktig eftersom den upprättar en kryptografiskt variabeln spårbarhet och undviker delning av Signeringsnycklar.

![img-Generic-CERT-chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Läs mer här så [skapa en certifikatkedja](iot-hub-security-x509-create-certificates.md#createcertchain) som görs när du registrerar enheter.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Så här registrerar du X.509 CA-certifikatet till IoT-hubb

Registrera X.509 Certifikatutfärdarens certifikat till IoT-hubb där den används för att autentisera dina enheter under registrering och anslutningen.  Registrera X.509 CA-certifikatet är en tvåstegsprocess som omfattar filuppladdning för certifikat och bevis på tillgång.

Överför signeras överför en fil som innehåller ditt certifikat.  Den här filen innehåller bör aldrig privata nycklar.

Konceptbevis tillgång steg omfattar en kryptografisk utmaning och process för svar på mellan dig och IoT-hubb.  Med hänsyn till att digitalt certifikat innehållet är offentlig och därför känslig för avlyssning, vill IoT-hubb säkerställa att du verkligen äger CA-certifikatet.  Det kan göra det genom att generera en slumpmässig utmaning som du måste logga med CA-certifikatet och motsvarande privata nyckel.  Om du behöll den privata nyckeln hemliga och skyddade som tidigare bäst, och du kommer endast ha kunskap för att slutföra det här steget. Hemlig privata nycklar är källan för förtroende med den här metoden.  När du har registrerat det en utmaningen att slutföra det här steget genom att överföra en fil som innehåller resultatet.

Läs mer här så [registrera ditt CA-certifikat](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Så här skapar du en enhet på IoT-hubb

Om du vill hindra enheten personifiering måste IoT-hubb du låta det vet vilka enheter du kan förvänta dig.  Det gör du genom att skapa en enhetspost i enhetsregistret för IoT-hubben.  Den här processen sker automatiskt när du använder IoT-hubb [enhet Etableringstjänsten](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (DP). 

Läs mer här så [manuellt skapa en enhet i IoT-hubb](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autentisera enheter signerad med X.509-certifikat

Vad som finns kvar är enhetsautentisering från med X.509 CA-certifikat som registrerats och enheter som har loggat in på en certifikatkedja med förtroenden när enheten ansluter även för första gången.  När ett X.509-CA-signerat enheten ansluter, den laddas upp dess certifikatkedja för verifiering. Kedjan innehåller alla mellanliggande certifikat för Certifikatutfärdaren och enheten.  Med den här informationen autentiserar IoT-hubb enheten i två steg.  IoT-hubb validerar kryptografiskt certifikatkedja för intern konsekvens och skickar en utmaning bevis av tillgång till enheten.  IoT-hubb deklarerar enheten autentiska på ett lyckat bevis av tillgång svar från enheten.  Deklarationen förutsätter att enhetens privata nyckeln skyddas och att endast enheten har kan svara på den här kontrollen.  Vi rekommenderar användning av säker modulerna som maskinvara säker moduler (HSM) i enheter som ska skydda privata nycklar.

En lyckad enhetsanslutning till IoT-hubb Slutför autentiseringen och är också indikation på en korrekt konfiguration.

Läs mer här så [slutföra det här steget för anslutning av enheten](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [värdet för autentisering med X.509 CA](iot-hub-x509ca-concept.md) i IoT.

Kom igång med IoT-hubb [enhet Etableringstjänsten](https://docs.microsoft.com/azure/iot-dps/).
