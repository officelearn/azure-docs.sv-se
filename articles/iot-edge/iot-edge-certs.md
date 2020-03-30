---
title: Certifikat för enhetssäkerhet – Azure IoT Edge | Microsoft-dokument
description: Azure IoT Edge använder certifikat för att validera enheter, moduler och bladenheter och upprätta säkra anslutningar mellan dem.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58294c7afdf31ddd29611351d6442db1c4966157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269035"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Förstå hur Azure IoT Edge använder certifikat

IoT Edge-certifikat används av modulerna och nedströms IoT-enheter för att verifiera identiteten och legitimiteten för [IoT Edge-hubbkörningsmodulen.](iot-edge-runtime.md#iot-edge-hub) Dessa verifieringar möjliggör en TLS -säkerhet (transportlagersäkerhet) säker anslutning mellan körningen, modulerna och IoT-enheterna. Precis som IoT Hub själv kräver IoT Edge en säker och krypterad anslutning från IoT-nedströmsenheter (eller löv) och IoT Edge-moduler. För att upprätta en säker TLS-anslutning visar IoT Edge-hubbmodulen en servercertifikatkedja för att ansluta klienter så att de kan verifiera sin identitet.

I den här artikeln beskrivs hur IoT Edge-certifikat kan fungera i produktions-, utvecklings- och testscenarier. Medan skripten är olika (Powershell vs bash), begreppen är desamma mellan Linux och Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certifikat

Vanligtvis är tillverkarna inte slutanvändare av en IoT Edge-enhet. Ibland är det enda förhållandet mellan de två när slutanvändaren, eller operatören, köper en generisk enhet som gjorts av tillverkaren. Andra gånger arbetar tillverkaren under kontrakt för att bygga en anpassad enhet för operatören. IoT Edge-certifikatdesignen försöker ta hänsyn till båda scenarierna.

Följande bild illustrerar IoT Edge användning av certifikat. Det kan finnas noll, ett eller många mellanliggande signeringscertifikat mellan rotcertifikatutfärdarcertifikatet och enhetscertifikatutfärdaren, beroende på antalet inblandade entiteter. Här visar vi ett fall.

![Diagram över typiska certifikatrelationer](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certifikatutfärdare

Certifikatutfärdaren, eller certifikatutfärdaren för korta, är en enhet som utfärdar digitala certifikat. En certifikatutfärdare fungerar som en betrodd tredje part mellan ägaren och mottagaren av certifikatet. Ett digitalt certifikat intygar att certifikatmottagaren äger en offentlig nyckel. Certifikatkedjan för förtroende fungerar genom att först utfärda ett rotcertifikat, vilket är grunden för förtroende för alla certifikat som utfärdats av myndigheten. Efteråt kan ägaren använda rotcertifikatet för att utfärda ytterligare mellanliggande certifikat (leaf-certifikat).

### <a name="root-ca-certificate"></a>Rotcertifikatutfärdarcertifikat

Ett rotcertifikatutfärdarcertifikat är roten till förtroendet för hela processen. I produktionsscenarier köps det här certifikatutfärdarcertifikatet vanligtvis från en betrodd kommersiell certifikatutfärdare som Baltimore, Verisign eller DigiCert. Om du har fullständig kontroll över de enheter som ansluter till dina IoT Edge-enheter är det möjligt att använda en certifikatutfärdar på företagsnivå. I båda fallen rullas hela certifikatkedjan från IoT Edge-hubben upp till den, så leaf IoT-enheterna måste lita på rotcertifikatet. Du kan lagra rotcertifikatutfärdarcertifikatet antingen i det betrodda rotcertifikatutfärdararkivet eller ange certifikatinformationen i programkoden.

### <a name="intermediate-certificates"></a>Mellanliggande certifikat

I en typisk tillverkningsprocess för att skapa säkra enheter används rotcertifikatutfärdare sällan direkt, främst på grund av risken för läckage eller exponering. Rotcertifikatutfärdarcertifikatet skapar och signerar digitalt ett eller flera mellanliggande CA-certifikat. Det får bara finnas en, eller så kan det finnas en kedja av dessa mellanliggande certifikat. Scenarier som skulle kräva en kedja av mellanliggande certifikat inkluderar:

* En hierarki av avdelningar inom en tillverkare.

* Flera företag involverade seriellt i produktionen av en enhet.

* En kund som köper en rotcertifikatutfärdare och härleder ett signeringscertifikat som tillverkaren kan signera de enheter de gör för kundens räkning.

Under alla omständigheter använder tillverkaren ett mellanliggande certifikatutfärdarcertifikat i slutet av den här kedjan för att signera enhetscertifikatcertifikatet som placerats på slutenheten. I allmänhet är dessa mellanliggande certifikat noga bevakade vid tillverkningsanläggningen. De genomgår strikta processer, både fysiska och elektroniska för sin användning.

### <a name="device-ca-certificate"></a>Certifikat för enhetscertifikatutfärdarcertifikat

Enhetscertifikatutfärdarens certifikat genereras från och signeras av det slutliga mellanliggande certifikatutfärdarcertifikatet i processen. Det här certifikatet installeras på själva IoT Edge-enheten, helst i säker lagring, till exempel en maskinvarusäkerhetsmodul (HSM). Dessutom identifierar ett enhetscertifikatutfärdarcertifikat unikt en IoT Edge-enhet. Enhetscertifikatutfärdarens certifikat kan signera andra certifikat.

### <a name="iot-edge-workload-ca"></a>Certifikatutfärdaren för IoT Edge-arbetsbelastning

[IoT Edge Security Manager](iot-edge-security-manager.md) genererar arbetsbelastningens CA-certifikat, det första på operatörssidan av processen, när IoT Edge startar. Det här certifikatet genereras från och signeras av "enhetscertifikatutfärdarens certifikat". Det här certifikatet, som bara är ett annat mellanliggande signeringscertifikat, används för att generera och signera andra certifikat som används av IoT Edge-körningen. Idag är det främst IoT Edge-hubbservercertifikatet som beskrivs i följande avsnitt, men i framtiden kan inkludera andra certifikat för autentisering av IoT Edge-komponenter.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge-hubbservercertifikat

IoT Edge-hubbservercertifikatet är det faktiska certifikat som presenteras för bladenheter och moduler för identitetsverifiering under etableringen av TLS-anslutningen som krävs av IoT Edge. Det här certifikatet visar hela kedjan med signeringscertifikat som används för att generera det upp till rotcertifikatutfärdarcertifikatet, som leaf IoT-enheten måste lita på. När det skapas av IoT Edge Security Manager anges det vanliga namnet (CN) för det här IoT Edge-hubbcertifikatet till egenskapen hostname i filen config.yaml efter konvertering till gemener. Den här konfigurationen är en vanlig källa till förväxling med IoT Edge.

## <a name="production-implications"></a>Produktionskonsekvenser

En rimlig fråga kan vara "varför IoT Edge behöver "arbetsbelastning CA" extra certifikat? Kan den inte använda enhetscertifikatutfärdaren för att direkt generera IoT Edge-hubbservercertifikatet?". Tekniskt sett kan det. Syftet med detta mellanliggande certifikat för arbetsbelastning är dock att skilja mellan problem mellan enhetstillverkaren och enhetsoperatören. Föreställ dig ett scenario där en IoT Edge-enhet säljs eller överförs från en kund till en annan. Du skulle förmodligen vilja att enheten CA certifikat som tillhandahålls av tillverkaren ska vara oföränderlig. Arbetsbelastningscertifikaten som är specifika för driften av enheten bör dock rensas och återskapas för den nya distributionen.

Eftersom tillverknings- och driftprocesser är åtskilda bör du tänka på följande konsekvenser när du förbereder produktionsenheter:

* Med alla certifikatbaserade processer bör rotcertifikatutfärdarcertifikatet och alla mellanliggande CA-certifikat skyddas och övervakas under hela processen med att distribuera en IoT Edge-enhet. IoT Edge-enhetstillverkaren bör ha starka processer på plats för korrekt lagring och användning av sina mellanliggande certifikat. Dessutom bör enhetscertifikatutfärdarens certifikat förvaras så säkert som möjligt på själva enheten, helst en säkerhetsmodul för maskinvara.

* IoT Edge-hubbservercertifikatet visas av IoT Edge-hubben för anslutningsklientenheter och moduler. Det gemensamma namnet (CN) för enhetscertifikatutfärdaren **får inte vara** detsamma som "värdnamnet" som ska användas i config.yaml på IoT Edge-enheten. Namnet som används av klienter för att ansluta till IoT Edge (till exempel via parametern GatewayHostName i anslutningssträngen eller CONNECT-kommandot i MQTT) **kan inte vara** detsamma som det vanliga namnet som används i enhetscertifikatutfärdarcertifikatet. Den här begränsningen beror på att IoT Edge-hubben presenterar hela sin certifikatkedja för verifiering av klienter. Om IoT Edge-hubbservercertifikatet och enhetscertifikatutfärdarens certifikat båda har samma CN får du i ett verifieringsloop och certifikatet ogiltigförklarar.

* Eftersom enhetscertifikatutfärdarens certifikat används av säkerhetsdemonen IoT Edge för att generera de slutliga IoT Edge-certifikaten, måste det själv vara ett signeringscertifikat, vilket innebär att det har certifikatsigneringsfunktioner. Om du använder "V3 Basic constraints CA:True" på enhetscertifikatutfärdarens certifikat ställer du automatiskt in de nödvändiga tangentanvändningsegenskaperna.

>[!Tip]
> Om du redan har gått igenom installationen av IoT Edge som en transparent gateway i ett scenario med dev/test med hjälp av våra "bekvämlighetsskript" (se nästa avsnitt) och använde samma värdnamn när du skapade enhetscertifikatutfärdarens certifikat som du gjorde för värdnamnet i config.yaml, kanske du undrar varför det fungerade. I ett försök att förenkla utvecklarupplevelsen lägger bekvämlighetsskripten till ett ".ca" i slutet av namnet du skickar in i skriptet. Så, till exempel, om du använde "mygateway" för både ditt enhetsnamn i skript och värdnamn i config.yaml, kommer den förra att förvandlas till mygateway.ca innan de används som CN för enheten CA cert.

## <a name="devtest-implications"></a>Konsekvenser för utveckling/test

För att underlätta utveckling och testscenarier tillhandahåller Microsoft en uppsättning [bekvämlighetsskript](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) för att generera certifikat som inte är produktionscertifikat som är lämpliga för IoT Edge i det genomskinliga gateway-scenariot. Exempel på hur skripten fungerar finns i [Skapa democertifikat för att testa IoT Edge-enhetsfunktioner](how-to-create-test-certificates.md).

>[!Tip]
> Om du vill ansluta enhetens IoT-enheter och -program som använder vår IoT-enhet SDK via IoT Edge måste du lägga till parametern GatewayHostName (tillval) i slutet av enhetens anslutningssträng. När Edge Hub Server-certifikatet genereras baseras det på en gemytsversion av värdnamnet från config.yaml, därför bör du ange parametern GatewayHostName i gemener för att namnen ska matcha och TLS-certifikatverifieringen ska lyckas.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exempel på IoT Edge-certifikathierarki

För att illustrera ett exempel på den här certifikatsökvägen kommer följande skärmbild från en fungerande IoT Edge-enhet som konfigurerats som en genomskinlig gateway. OpenSSL används för att ansluta till IoT Edge-hubben, validera och dumpa certifikaten.

![Skärmbild av certifikathierarkin på varje nivå](./media/iot-edge-certs/iotedge-cert-chain.png)

Du kan se hierarkin av certifikatdjup som representeras i skärmbilden:

| Rotcertifikatutfärdarcertifikat         | Endast Azure IoT Hub CA Cert-certifikattest                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Mellanliggande ca-certifikat | Endast Azure IoT Hub Intermediate Cert-test                                                                 |
| Certifikat för enhetscertifikatutfärdarcertifikat       | iotgateway.ca ("iotgateway" skickades in som < gateway värdnamn > till bekvämlighet skript)   |
| Ca-certifikat för arbetsbelastning     | iotedge arbetsbelastning ca                                                                                       |
| IoT Edge Hub Server-certifikat | iotedgegw.local (matchar "värdnamnet" från config.yaml)                                            |

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-moduler](iot-edge-modules.md)

[Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
