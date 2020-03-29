---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748792"
---
Den här artikeln innehåller nästa detaljnivå för att skydda Azure IoT-baserad IoT-baserad IoT-infrastruktur (IoT). Den länkar till information på implementeringsnivå för att konfigurera och distribuera varje komponent. Det ger också jämförelser och val mellan olika konkurrerande metoder.

Skydda Azure IoT-distributionen kan delas in i följande tre säkerhetsområden:

* **Enhetssäkerhet:** Säkra IoT-enheten medan den distribueras i naturen.

* **Anslutningssäkerhet**: Att se till att alla data som överförs mellan IoT-enheten och IoT Hub är konfidentiellt och manipuleringssäkert.

* **Molnsäkerhet:** Tillhandahålla ett sätt att skydda data medan den går igenom och lagras i molnet.

![Tre säkerhetsområden](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Säker etablering och autentisering av enheter

IoT-lösningsacceleratorerna skyddar IoT-enheter med följande två metoder:

* Genom att tillhandahålla en unik identitetsnyckel (säkerhetstoken) för varje enhet, som kan användas av enheten för att kommunicera med IoT Hub.

* Genom att använda ett [X.509-certifikat](https://www.itu.int/rec/T-REC-X.509-201210-S) på enheten och privat nyckel som ett sätt att autentisera enheten till IoT Hub. Den här autentiseringsmetoden säkerställer att den privata nyckeln på enheten inte är känd utanför enheten när som helst, vilket ger en högre säkerhetsnivå.

Metoden för säkerhetstoken tillhandahåller autentisering för varje anrop som görs av enheten till IoT Hub genom att associera den symmetriska nyckeln till varje anrop. X.509-baserad autentisering möjliggör autentisering av en IoT-enhet på det fysiska lagret som en del av TLS-anslutningsanläggningen. Den säkerhetstokenbaserade metoden kan användas utan X.509-autentiseringen, vilket är ett mindre säkert mönster. Valet mellan de två metoderna styrs främst av hur säker enhetsautentiseringen måste vara och tillgängligheten för säker lagring på enheten (för att lagra den privata nyckeln på ett säkert sätt).

## <a name="iot-hub-security-tokens"></a>Säkerhetstoken för IoT-hubben

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. Dessutom är säkerhetstoken begränsade i tidsgiltighet och omfattning. Azure IoT SDK:er genererar automatiskt token utan att kräva någon särskild konfiguration. Vissa scenarier kräver dock att användaren genererar och använder säkerhetstoken direkt. Dessa scenarier inkluderar direkt användning av MQTT-, AMQP- eller HTTP-ytorna eller implementeringen av tokentjänstmönstret.

Mer information om säkerhetstokens struktur och dess användning finns i följande artiklar:

* [Struktur för säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Använda SAS-token som en enhet](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Varje IoT-hubb har ett [identitetsregister](../articles/iot-hub/iot-hub-devguide-identity-registry.md) som kan användas för att skapa resurser per enhet i tjänsten, till exempel en kö som innehåller moln-till-enhet-meddelanden under flygning och för att ge åtkomst till de enhetsinriktade slutpunkterna. IoT Hub-identitetsregistret tillhandahåller säker lagring av enhetsidentiteter och säkerhetsnycklar för en lösning. Enskilda eller grupper av enhetsidentiteter kan läggas till i en tillåt-lista, eller en blockeringslista, vilket möjliggör fullständig kontroll över enhetsåtkomst. Följande artiklar innehåller mer information om identitetsregistrets struktur och åtgärder som stöds.

[IoT Hub stöder protokoll som MQTT, AMQP och HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Vart och ett av dessa protokoll använder säkerhetstoken från IoT-enheten till IoT Hub på olika sätt:

* AMQP: SASL PLAIN och AMQP`{policyName}@sas.root.{iothubName}` Claims-baserad säkerhet (med token på IoT-hubbnivå; `{deviceId}` med enhetsscoped token).

* MQTT: CONNECT-paket `{deviceId}` använder som `{ClientId}` `{IoThubhostname}/{deviceId}` , i fältet **Användarnamn** och en SAS-token i fältet **Lösenord.**

* HTTP: Giltig token finns i auktoriseringsbegäran huvudet.

IoT Hub-identitetsregister kan användas för att konfigurera säkerhetsautentiseringsuppgifter per enhet och åtkomstkontroll. Men om en IoT-lösning redan har en betydande investering i ett [anpassat enhetsidentitetsregister och/eller autentiseringsschema](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)kan den integreras i en befintlig infrastruktur med IoT Hub genom att skapa en tokentjänst.

### <a name="x509-certificate-based-device-authentication"></a>X.509 certifikatbaserad enhetsautentisering

Användningen av ett [enhetsbaserat X.509-certifikat](../articles/iot-hub/iot-hub-devguide-security.md) och dess associerade privata och offentliga nyckelpar möjliggör ytterligare autentisering på det fysiska lagret. Den privata nyckeln lagras säkert i enheten och kan inte identifieras utanför enheten. X.509-certifikatet innehåller information om enheten, till exempel enhets-ID och annan organisationsinformation. En signatur för certifikatet genereras med hjälp av den privata nyckeln.

Etableringsflöde på hög nivå:

* Associera en identifierare med en fysisk enhet – enhetsidentitet och/eller X.509-certifikat som är associerade till enheten under enhetstillverkning eller idrifttagning.

* Skapa en motsvarande identitetspost i IoT Hub – enhetsidentitet och tillhörande enhetsinformation i IoT Hub-identitetsregistret.

* Lagra X.509-certifikatets tumavtryck på ett säkert sätt i IoT Hub-identitetsregistret.

### <a name="root-certificate-on-device"></a>Rotcertifikat på enheten

När du upprättar en säker TLS-anslutning med IoT Hub autentiserar IoT-enheten IoT Hub med hjälp av ett rotcertifikat som är en del av enhetenSDK. För C-klienten SDK finns certifikatet\\under\\mappen "c certs" under roten till repo. Även om dessa rotcertifikat är långlivade, kan de fortfarande upphöra att gälla eller återkallas. Om det inte finns något sätt att uppdatera certifikatet på enheten kanske enheten inte kan ansluta till IoT Hub (eller någon annan molntjänst). Med ett sätt att uppdatera rotcertifikatet när IoT-enheten distribueras effektivt minskar denna risk.

## <a name="securing-the-connection"></a>Säkra anslutningen

Internetanslutningen mellan IoT-enheten och IoT Hub är skyddad med hjälp av TLS-standarden (Transport Layer Security). Azure IoT stöder [TLS 1.2,](https://tools.ietf.org/html/rfc5246)TLS 1.1 och TLS 1.0 i den här ordningen. Stöd för TLS 1.0 tillhandahålls endast för bakåtkompatibilitet. Kontrollera [TLS-stöd i IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) för att se hur du konfigurerar hubben så att det använder TLS 1.2, eftersom det ger mest säkerhet.

## <a name="securing-the-cloud"></a>Säkra molnet

Azure IoT Hub tillåter definition av [åtkomstkontrollprinciper](../articles/iot-hub/iot-hub-devguide-security.md) för varje säkerhetsnyckel. Den använder följande uppsättning behörigheter för att bevilja åtkomst till var och en av IoT Hub slutpunkter. Behörigheter begränsar åtkomsten till en IoT Hub baserat på funktioner.

* **RegistryRead**. Ger läsåtkomst till identitetsregistret. Mer information finns i [identitetsregistret](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Ger läs- och skrivåtkomst till identitetsregistret. Mer information finns i [identitetsregistret](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Ger åtkomst till molntjänstinriktade kommunikations- och övervakningsslutpunkter. Den ger till exempel behörighet till backend-molntjänster för att ta emot meddelanden från enhet till moln, skicka meddelanden från molnet till enheten och hämta motsvarande leveransbekräftelser.

* **DeviceConnect**. Ger åtkomst till enhetsvända slutpunkter. Den ger till exempel behörighet att skicka meddelanden från enhet till moln och ta emot meddelanden från molnet till enheten. Den här behörigheten används av enheter.

Det finns två sätt att hämta **DeviceConnect-behörigheter** med IoT Hub med [säkerhetstoken:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)med hjälp av en enhetsidentitetsnyckel eller en delad åtkomstnyckel. Dessutom är det viktigt att notera att alla funktioner som är tillgängliga från `/devices/{deviceId}`enheter exponeras av design på slutpunkter med prefix .

[Tjänstkomponenter kan bara generera säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) med hjälp av principer för delad åtkomst som ger rätt behörighet.

Azure IoT Hub och andra tjänster som kan vara en del av lösningen tillåter hantering av användare som använder Azure Active Directory.

Data som används av Azure IoT Hub kan förbrukas av en mängd olika tjänster som Azure Stream Analytics och Azure blob storage. Dessa tjänster ger åtkomst till hantering. Läs mer om dessa tjänster och tillgängliga alternativ:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): En skalbar, fullt indexerad databastjänst för halvstrukturerade data som hanterar metadata för de enheter som du etablerar, till exempel attribut, konfiguration och säkerhetsegenskaper. Azure Cosmos DB erbjuder högpresterande och högdataflödesbearbetning, schemaaoberoende indexering av data och ett omfattande SQL-frågegränssnitt.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Realtidsströmbearbetning i molnet som gör att du snabbt kan utveckla och distribuera en billig analyslösning för att upptäcka insikter i realtid från enheter, sensorer, infrastruktur och program. Data från den här fullständigt hanterade tjänsten kan skalas till valfri volym samtidigt som du uppnår hög dataflöde, låg latens och återhämtning.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): En molnplattform för att skapa kraftfulla webb- och mobilappar som ansluter till data var som helst. i molnet eller lokalt. Skapa engagerande mobilappar för iOS, Android och Windows. Integrera med din SaaS (Software as a Service) och företagsprogram med direktanslutning till dussintals molnbaserade tjänster och företagsprogram. Kod på ditt favoritspråk och IDE (.NET, Node.js, PHP, Python eller Java) för att skapa webbappar och API:er snabbare än någonsin.

* [Logikappar](https://azure.microsoft.com/services/app-service/logic/): Logic Apps-funktionen i Azure App Service hjälper dig att integrera din IoT-lösning till dina befintliga affärssystem och automatisera arbetsflödesprocesser. Logic Apps gör det möjligt för utvecklare att utforma arbetsflöden som startar från en utlösare och sedan utföra en rad steg – regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärsprocesser. Logic Apps erbjuder direktanslutning till ett stort ekosystem av SaaS-, molnbaserade och lokala program.

* [Azure Blob-lagring:](https://azure.microsoft.com/services/storage/)Tillförlitlig, ekonomisk molnlagring för de data som dina enheter skickar till molnet.

## <a name="conclusion"></a>Slutsats

Den här artikeln innehåller en översikt över information om implementeringsnivå för att utforma och distribuera en IoT-infrastruktur med Azure IoT. Konfigurera varje komponent som säker är nyckeln till att skydda den övergripande IoT-infrastrukturen. De designval som finns i Azure IoT ger en viss nivå av flexibilitet och valfrihet. Varje val kan dock få säkerhetskonsekvenser. Det rekommenderas att vart och ett av dessa val utvärderas genom en risk/kostnadsbedömning.
