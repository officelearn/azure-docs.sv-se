---
title: Certifikat för enhetssäkerhet – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge använder certifikat för att verifiera enheter, moduler och lövenheter och upprätta säkra anslutningar mellan dem.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 91cde6965f3635d6d2acfaf581f570779020f8ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445323"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Kostnadsinformation för Azure IoT Edge-certifikat

IoT Edge-certifikat används för moduler och efterföljande IoT-enheter och kontrollera identitet och giltighet den [IoT Edge hub](iot-edge-runtime.md#iot-edge-hub) headertoquerystring som de ansluter till. Dessa kontroller kan en TLS (transport layer security) säker anslutning mellan körningen, moduler och IoT-enheter. Precis som IoT Hub själva, IoT Edge kräver en säker och krypterad anslutning från IoT nedströms (eller blad) enheter och IoT Edge-moduler. För att upprätta en säker TLS-anslutning, presenterar IoT Edge hub-modul en server certifikatkedjan anslutande klienter för att verifiera sin identitet.

Den här artikeln förklarar hur IoT Edge-certifikat kan arbeta i produktion, utveckling och testscenarion. Skripten är olika (Powershell jämfört med bash), är begreppen likadana mellan Linux och Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certifikat

Tillverkare är vanligtvis inte användarna av en IoT Edge-enhet. Ibland är den enda relationen mellan två när slutanvändaren eller operatorn köper en allmän enhet som gjorts av tillverkaren. Andra gånger fungerar tillverkaren enligt avtal att skapa en anpassad enhet åt operatorn. Design för IoT Edge-certifikatet försöker att ta hänsyn till båda scenarierna.

Följande bild visar IoT Edge-användningen av certifikat. Det kan vara inget, ett eller flera mellanliggande certifikat för tokensignering mellan rot-CA-certifikatet och certifikatutfärdare enheten, beroende på antalet enheter som ingår. Här visar vi ett enda fall.

![Diagram över vanliga certifikat relationer](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certifikatutfärdare

Certifikatutfärdare eller ”CA” för kort, är en entitet som utfärdar digitala certifikat. En certifikatutfärdare fungerar som en betrodd tredje part mellan ägare och mottagaren av certifikatet. Ett digitalt certifikat certifierar ägarskap för en offentlig nyckel av mottagare för certifikatet. Certifikatkedja med förtroenden fungerar genom att först utfärda ett rotcertifikat som ligger till grund för förtroende för alla certifikat som utfärdats av behörighet. Ägare kan därefter använda rotcertifikatet för att utfärda ytterligare mellanliggande certifikat (”löv” certifikat).

### <a name="root-ca-certificate"></a>Rotcertifikatutfärdarcertifikat

Certifikat från en rotcertifikatutfärdare är förtroenderoten för hela processen. I produktionsscenarier bör köps vanligtvis detta CA-certifikat från en betrodd kommersiell certifikatutfärdare som Baltimore, Verisign eller DigiCert. Om du har fullständig kontroll över de enheter som ansluter till IoT Edge-enheter, är det möjligt att använda företagets på vilken certifikatutfärdare. I vilket fall samlar hela certifikatkedjan från IoT Edge hub upp in, så att IoT lövenheter måste lita på rotcertifikatet. Du kan lagra certifikatet för rotcertifikatutfärdaren i arkivet Betrodda rotcertifikat-utfärdare eller ange information om certifikat i programkoden.

### <a name="intermediate-certificates"></a>Mellanliggande certifikat

I en typisk process för att skapa säkra enheter, används sällan rotcertifikatutfärdarens certifikat direkt, främst på grund av risken för dataläckage eller exponering. Certifikatet för rotcertifikatutfärdaren skapar och signerar ett eller flera mellanliggande CA-certifikat. Det kan bara finnas en eller det kan finnas en kedja av dessa mellanliggande certifikat. Scenarier som kräver en kedja av mellanliggande certifikat är:

* En hierarki av avdelningar inom en tillverkare.

* Flera företag seriellt i produktionen av en enhet.

* En kund köpa en rot-CA och som följer ett signeringscertifikat för tillverkaren att registrera enheterna som de gör för kundens räkning.

Tillverkaren använder dock en mellanliggande certifikatutfärdare i slutet av kedjan för att registrera enheten CA-certifikatet för end-enheten. I allmänhet bör skyddade dessa mellanliggande certifikat nära på tillverkningsanläggningen. De genomgår strikta processer, både fysiska och elektronisk för deras användning.

### <a name="device-ca-certificate"></a>Enhets-CA-certifikat

Enheten CA-certifikatet genereras från och signerade av det slutliga mellanliggande CA-certifikatet i processen. Det här certifikatet installeras på IoT Edge-enheten, helst i säker lagring, till exempel en maskinvarusäkerhetsmodul (HSM). Certifikat från en enhet identifierar dessutom en IoT Edge-enhet. Enheten CA-certifikat kan utfärda andra certifikat för IoT Edge. Till exempel utfärdar enhetens CA-certifikat löv enhetscertifikat som används för att autentisera enheter till den [Azure IoT Device Provisioning Service](../iot-dps/about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>IoT Edge-arbetsbelastning CA

Den [IoT Edge Security Manager](iot-edge-security-manager.md) genererar arbetsbelastning CA-certifikat, först på den ”-operatör”-delen av processen, när IoT Edge startas första gången. Det här certifikatet genereras från och signerade av ”enheten certifikatutfärdarcertifikat”. Det här certifikatet som är en mellanliggande signeringscertifikat, används för att skapa och signera andra certifikat som används av IoT Edge-körningen. Idag, som är främst IoT Edge hub servercertifikatet beskrivs i följande avsnitt, men i framtiden kan omfatta andra certifikat för att autentisera IoT Edge-komponenter.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub-servercertifikat

IoT Edge hub servercertifikatet är det faktiska certifikatet lövenheter och bekräfta din identitet vid upprättandet av TLS-anslutning som krävs av IoT Edge-moduler. Det här certifikatet anger fullständig kedja för att signera certifikat som används för att generera upp till rotcertifikatutfärdarens certifikat, som måste ha förtroende för löv IoT-enheter. När genererats av IoT Edge Security Manager, nätverksnamn (CN) för denna IoT Edge hub har certifikat angetts till hostname-egenskapen i filen config.yaml efter konverteringen till gemener. Det här är en gemensam källa för förväxling med IoT Edge.

## <a name="production-implications"></a>Konsekvenser för produktion

En rimlig fråga kan vara ”Varför behöver IoT Edge-arbetsbelastningen CA-extra certifikat? Det gick inte att den använda enheten CA-certifikatet direkt generera IoT Edge hub servercertifikatet ”?. Tekniskt sett kan. Syftet med den här ”arbetsbelastning” mellanliggande certifikat är dock att separata frågor mellan enhetstillverkaren och operatorn enheten. Tänk dig ett scenario där en IoT Edge-enhet är säljs eller överföras från en kund till en annan. Du vill förmodligen enheten CA-certifikatet som tillhandahålls av tillverkaren vara inte kan ändras. Men ska de ”arbetsbelastning” certifikat som är specifika för driften av enheten rensas och återskapas för den nya distributionen.

Eftersom tillverkning och åtgärden processer är åtskilda, Överväg följande faktorer när du förbereder produktionsenheter:

* Med andra certifikatbaserad processer ska certifikatet för rotcertifikatutfärdaren och mellanliggande CA-certifikat skyddas och övervakas under hela processen med att lansera en IoT Edge-enhet. IoT Edge-enhetens tillverkare bör ha starka processer på plats för rätt lagring och användningen av sin mellanliggande certifikat. Dessutom kan bör enheten CA-certifikat hållas som säker lagring som möjligt på själva enheten, helst en maskinvarusäkerhetsmodul.

* Servercertifikatet för IoT Edge hub presenteras av IoT Edge hub att anslutande klientenheter och moduler. Nätverksnamn (CN) för enheten CA-certifikatet **får inte vara** samma som ”värdnamnet” som ska användas i config.yaml på IoT Edge-enhet. Det namn som används av klienter för att ansluta till IoT Edge (till exempel via parametern GatewayHostName till anslutningssträngen eller kommandot CONNECT i MQTT) **får inte vara** samma som eget namn som används i enhetens CA-certifikat. Den här begränsningen beror på IoT Edge-hubben visas dess hela certifikatkedjan för verifiering av klienter. Om servercertifikatet IoT Edge hub och device CA-certifikatet har båda samma CN, du får i en loop för verifiering och certifikatet blir ogiltiga.

* Eftersom enheten CA-certifikatet används av IoT Edge security daemon för att generera de slutliga IoT Edge-certifikat, måste det själva vara ett signeringscertifikat, vilket innebär att den har funktioner för certifikatsignering. Tillämpa ”V3 grundläggande begränsningar CA:True” på enhetens CA-certifikat automatiskt ställer in egenskaperna krävs nyckelanvändning.

>[!Tip]
> Om du har redan gått igenom installationen av IoT Edge som en transparent gateway i ett scenario för utveckling/testning med hjälp av vår ”bekvämlighet skript” (se nästa avsnitt) och används samma värdnamn när du skapar enhetscertifikat CA: N som du gjorde för värdnamnet i config.yaml , du kanske undrar varför det fungerade. I syfte att förenkla utvecklarupplevelsen, bekvämlighet skript lägger du till en ”.ca” i slutet av namnet som du skickar till skriptet. Så, till exempel om du har använt ”mygateway” för båda dina enhetsnamn i skripten och värdnamn i config.yaml tidigare kommer att omvandlas till mygateway.ca innan de används som CN för enheten CA-certifikat.

## <a name="devtest-implications"></a>Konsekvenser för utveckling/testning

För att underlätta utveckling och testscenarion, Microsoft tillhandahåller en uppsättning [bekvämlighet skript](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) för att skapa icke-produktion-certifikat som är lämpliga för IoT Edge i transparent gateway-scenario. Exempel på hur skripten fungerar finns i [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md).

Dessa skript att generera som följer kedjan Certifikatstrukturen beskrivs i den här artikeln. Följande kommandon generera ”rotcertifikatutfärdarens certifikat” och en enda ”mellanliggande CA-certifikat”.

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

På samma sätt kan generera kommandona ”enheten certifikatutfärdarcertifikat”.

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* Den **\<gateway enhetsnamn\>** skickas till skripten **får inte** vara samma som ”värdnamnet”-parametern i config.yaml. Skripten hjälpa dig att undvika eventuella problem genom att lägga till en ”.ca” sträng i **\<gateway enhetsnamn\>** att förhindra att namn-kollision om en användare ställer in IoT Edge med samma namn på båda platserna. Men är det bra att undvika att använda samma namn.

>[!Tip]
> Om du vill ansluta din enhet IoT ”löv” enheter och program som använder våra IoT-enhetens SDK via IoT Edge, måste du lägga till den valfria parametern GatewayHostName till slutet av enhetens anslutningssträng. När Edge Hub certifikatet skapas, den är baserad på en alltid i lägre version av värdnamnet från config.yaml, därför för som ska matcha och TLS-certifikatverifiering ska lyckas, du bör ange parametern GatewayHostName i gemener.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exempel på IoT Edge-certifikathierarkin

Skärmbilden nedan är från en fungerande IoT Edge-enhet som är konfigurerad som en transparent gateway för att illustrera ett exempel på den här sökvägen för certifikatet. OpenSSL används för att ansluta till IoT Edge-hubben, verifiera och dumpa ut certifikaten.

![Skärmbild av certifikathierarkin på varje nivå](./media/iot-edge-certs/iotedge-cert-chain.png)

Du kan se hierarkin för certifikatet djup visas i skärmbilden:

| Rotcertifikatutfärdarcertifikat         | Azure IoT Hub CA-certifikat Test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Mellanliggande CA-certifikat | Azure IoT Hub mellanliggande certifikat Test                                                                 |
| Enhets-CA-certifikat       | iotgateway.ca (”iotgateway” som skickades var i < gateway värdnamn > bekvämlighet skript)      |
| Arbetsbelastningen CA-certifikat     | iotedge arbetsbelastning ca                                                                                       |
| IoT Edge Hub Server Certificate | iotedgegw.Local (överensstämmer med värdnamnet från config.yaml)                                                |

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-moduler](iot-edge-modules.md)

[Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md)