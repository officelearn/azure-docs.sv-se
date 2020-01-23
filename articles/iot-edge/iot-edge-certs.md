---
title: Certifikat för enhetssäkerhet – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge använder certifikat för att verifiera enheter, moduler och lövenheter och upprätta säkra anslutningar mellan dem.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a222f72e705184c5a7ba6701cfda41073c7eba57
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548755"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Förstå hur Azure IoT Edge använder certifikat

IoT Edge certifikat används av modulerna och underordnade IoT-enheter för att verifiera identiteten och giltighet för modulen för [IoT Edge Hub](iot-edge-runtime.md#iot-edge-hub) -körning. Dessa kontroller kan en TLS (transport layer security) säker anslutning mellan körningen, moduler och IoT-enheter. Precis som IoT Hub själva, IoT Edge kräver en säker och krypterad anslutning från IoT nedströms (eller blad) enheter och IoT Edge-moduler. För att upprätta en säker TLS-anslutning visar IoT Edge Hub-modulen en server certifikat kedja för att ansluta klienter för att verifiera identiteten.

I den här artikeln förklaras hur IoT Edge certifikat kan fungera i produktions-, utvecklings-och test scenarier. Skripten är olika (Powershell jämfört med bash), är begreppen likadana mellan Linux och Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certifikat

Tillverkare är vanligt vis inte slutanvändare av en IoT Edge enhet. Ibland är den enda relationen mellan två när slutanvändaren eller operatören köper en generisk enhet som tillverkas av tillverkaren. Vid andra tillfällen fungerar tillverkaren enligt avtal för att bygga en anpassad enhet för operatören. Design för IoT Edge-certifikatet försöker att ta hänsyn till båda scenarierna.

Följande bild visar IoT Edge-användningen av certifikat. Det kan finnas noll, ett eller flera mellanliggande signerings certifikat mellan rot certifikat utfärdarens certifikat och enhetens CA-certifikat, beroende på antalet enheter som berörs. Här visar vi ett ärende.

![Diagram över vanliga certifikat relationer](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certifikatutfärdare

Certifikatutfärdare eller ”CA” för kort, är en entitet som utfärdar digitala certifikat. En certifikatutfärdare fungerar som en betrodd tredje part mellan ägare och mottagaren av certifikatet. Ett digitalt certifikat certifierar ägarskap för en offentlig nyckel av mottagare för certifikatet. Certifikatkedja med förtroenden fungerar genom att först utfärda ett rotcertifikat som ligger till grund för förtroende för alla certifikat som utfärdats av behörighet. Ägare kan därefter använda rotcertifikatet för att utfärda ytterligare mellanliggande certifikat (”löv” certifikat).

### <a name="root-ca-certificate"></a>Rotcertifikatutfärdarcertifikat

Certifikat från en rotcertifikatutfärdare är förtroenderoten för hela processen. I produktions scenarier köps det här CA-certifikatet vanligt vis från en betrodd kommersiell certifikat utfärdare som Baltimore, VeriSign eller DigiCert. Om du har fullständig kontroll över de enheter som ansluter till dina IoT Edge enheter, är det möjligt att använda en certifikat utfärdare på företags nivå. I båda fall samlar hela certifikat kedjan från IoT Edge Hub upp till den, så att noderna för löv-IoT måste ha förtroende för rot certifikatet. Du kan lagra rot certifikat utfärdarens certifikat antingen i arkivet för betrodda rot certifikat utfärdare eller ange certifikat information i program koden.

### <a name="intermediate-certificates"></a>Mellanliggande certifikat

I en typisk process för att skapa säkra enheter, används sällan rotcertifikatutfärdarens certifikat direkt, främst på grund av risken för dataläckage eller exponering. Rot-CA-certifikatet skapar och signerar ett eller flera mellanliggande CA-certifikat digitalt. Det kan bara finnas en eller det kan finnas en kedja av dessa mellanliggande certifikat. Scenarier som kräver en kedja av mellanliggande certifikat är:

* En hierarki av avdelningar inom en tillverkare.

* Flera företag seriellt i produktionen av en enhet.

* En kund köpa en rot-CA och som följer ett signeringscertifikat för tillverkaren att registrera enheterna som de gör för kundens räkning.

Tillverkaren använder dock en mellanliggande certifikatutfärdare i slutet av kedjan för att registrera enheten CA-certifikatet för end-enheten. I allmänhet bör skyddade dessa mellanliggande certifikat nära på tillverkningsanläggningen. De genomgår strikta processer, både fysiska och elektronisk för deras användning.

### <a name="device-ca-certificate"></a>Enhets-CA-certifikat

Enheten CA-certifikatet genereras från och signerade av det slutliga mellanliggande CA-certifikatet i processen. Det här certifikatet installeras på den IoT Edge själva enheten, helst i säkert lagrings utrymme som en HSM-modul (Hardware Security Module). Certifikat från en enhet identifierar dessutom en IoT Edge-enhet. Enhetens CA-certifikat kan signera andra certifikat.

### <a name="iot-edge-workload-ca"></a>IoT Edge-arbetsbelastning CA

[IoT Edge Security Manager](iot-edge-security-manager.md) genererar arbets Belastningens CA-certifikat, det första på "operatörs sidan" i processen, när IoT Edge startas första gången. Det här certifikatet genereras från och signerade av ”enheten certifikatutfärdarcertifikat”. Det här certifikatet som är en mellanliggande signeringscertifikat, används för att skapa och signera andra certifikat som används av IoT Edge-körningen. Idag är det i första hand det IoT Edge Hub-servercertifikat som diskuteras i följande avsnitt, men i framtiden kan det inkludera andra certifikat för autentisering av IoT Edge-komponenter.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge Hub-servercertifikat

IoT Edge Hub-servercertifikat är det faktiska certifikatet som presenteras för löv enheter och moduler för identitets verifiering vid upprättandet av den TLS-anslutning som krävs av IoT Edge. Det här certifikatet anger fullständig kedja för att signera certifikat som används för att generera upp till rotcertifikatutfärdarens certifikat, som måste ha förtroende för löv IoT-enheter. När det genereras av IoT Edge Security Manager anges det egna namnet (CN) för det här IoT Edge Hub-certifikatet till egenskapen hostname i filen config. yaml efter konvertering till gemener. Den här konfigurationen är en gemensam källa för förvirring med IoT Edge.

## <a name="production-implications"></a>Konsekvenser för produktion

En rimlig fråga kan vara ”Varför behöver IoT Edge-arbetsbelastningen CA-extra certifikat? Det gick inte att använda enhetens CA-certifikat för att direkt generera IoT Edge Hubbs Server certifikatet? ". Tekniskt sett kan. Syftet med den här ”arbetsbelastning” mellanliggande certifikat är dock att separata frågor mellan enhetstillverkaren och operatorn enheten. Tänk dig ett scenario där en IoT Edge-enhet är säljs eller överföras från en kund till en annan. Du vill förmodligen enheten CA-certifikatet som tillhandahålls av tillverkaren vara inte kan ändras. Men ska de ”arbetsbelastning” certifikat som är specifika för driften av enheten rensas och återskapas för den nya distributionen.

Eftersom tillverknings-och drift processerna är åtskilda bör du tänka på följande när du förbereder produktions enheter:

* Med andra certifikatbaserad processer ska certifikatet för rotcertifikatutfärdaren och mellanliggande CA-certifikat skyddas och övervakas under hela processen med att lansera en IoT Edge-enhet. IoT Edge-enhetens tillverkare bör ha starka processer på plats för rätt lagring och användningen av sin mellanliggande certifikat. Dessutom kan bör enheten CA-certifikat hållas som säker lagring som möjligt på själva enheten, helst en maskinvarusäkerhetsmodul.

* IoT Edge Hub-servercertifikatet presenteras av IoT Edge Hub till de anslutande klient enheterna och modulerna. Det egna namnet (CN) för enhetens CA-certifikat **får inte vara** detsamma som "hostname" som ska användas i config. yaml på den IoT Edge enheten. Namnet som används av klienter för att ansluta till IoT Edge (till exempel via GatewayHostName-parametern för anslutnings strängen eller kommandot CONNECT i MQTT) **kan inte vara** samma som det gemensamma namnet som används i ENHETens CA-certifikat. Den här begränsningen beror på att IoT Edge Hub visar hela certifikat kedjan för verifiering av klienter. Om IoT Edge Hub-servercertifikat och enhetens CA-certifikat båda har samma CN, får du i en verifierings slinga och certifikatet blir ogiltig.

* Eftersom enhetens CA-certifikat används av IoT Edge Security daemon för att generera de slutgiltiga IoT Edge certifikaten, måste det vara ett signerings certifikat, vilket innebär att det har funktioner för certifikat signering. När du använder "v3 Basic-begränsningar CA: true" i enhetens CA-certifikat konfigureras automatiskt de nödvändiga egenskaperna för nyckel användning.

>[!Tip]
> Om du har redan gått igenom installationen av IoT Edge som en transparent gateway i ett scenario för utveckling/testning med hjälp av vår ”bekvämlighet skript” (se nästa avsnitt) och används samma värdnamn när du skapar enhetscertifikat CA: N som du gjorde för värdnamnet i config.yaml , du kanske undrar varför det fungerade. I syfte att förenkla utvecklarupplevelsen, bekvämlighet skript lägger du till en ”.ca” i slutet av namnet som du skickar till skriptet. Så, till exempel om du har använt ”mygateway” för båda dina enhetsnamn i skripten och värdnamn i config.yaml tidigare kommer att omvandlas till mygateway.ca innan de används som CN för enheten CA-certifikat.

## <a name="devtest-implications"></a>Konsekvenser för utveckling/testning

För att underlätta utveckling och testscenarion, Microsoft tillhandahåller en uppsättning [bekvämlighet skript](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) för att skapa icke-produktion-certifikat som är lämpliga för IoT Edge i transparent gateway-scenario. Exempel på hur skripten fungerar finns i [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md).

>[!Tip]
> Om du vill ansluta din enhet IoT ”löv” enheter och program som använder våra IoT-enhetens SDK via IoT Edge, måste du lägga till den valfria parametern GatewayHostName till slutet av enhetens anslutningssträng. När Edge Hub certifikatet skapas, den är baserad på en alltid i lägre version av värdnamnet från config.yaml, därför för som ska matcha och TLS-certifikatverifiering ska lyckas, du bör ange parametern GatewayHostName i gemener.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exempel på IoT Edge-certifikathierarkin

Skärmbilden nedan är från en fungerande IoT Edge-enhet som är konfigurerad som en transparent gateway för att illustrera ett exempel på den här sökvägen för certifikatet. OpenSSL används för att ansluta till IoT Edge Hub, validera och dumpa certifikaten.

![Skärmbild av certifikathierarkin på varje nivå](./media/iot-edge-certs/iotedge-cert-chain.png)

Du kan se hierarkin för certifikatet djup visas i skärmbilden:

| Rotcertifikatutfärdarcertifikat         | Azure IoT Hub CA-certifikat Test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Mellanliggande CA-certifikat | Azure IoT Hub mellanliggande certifikat Test                                                                 |
| Enhets-CA-certifikat       | iotgateway.ca (”iotgateway” som skickades var i < gateway värdnamn > bekvämlighet skript)   |
| Arbetsbelastningen CA-certifikat     | iotedge arbetsbelastning ca                                                                                       |
| IoT Edge Hub-servercertifikat | iotedgegw.Local (överensstämmer med värdnamnet från config.yaml)                                            |

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-moduler](iot-edge-modules.md)

[Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
