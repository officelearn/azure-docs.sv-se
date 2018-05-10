---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7d7cd8a197a89781a75f47bb4b4e2ec8fe7c3cb4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="secure-your-iot-deployment"></a>Skydda distributionen av IoT

Den här artikeln innehåller nästa detaljnivå för att skydda Azure IoT-baserade Sakernas Internet (IoT)-infrastruktur. Den länkar till implementeringsinformation för att konfigurera och distribuera varje komponent. Det ger också jämförelser och val mellan olika konkurrerande metoder.

Säkra Azure IoT-distributionen kan delas in i följande tre områden:

* **Enheternas säkerhet**: skydda IoT-enhet när den har distribuerats i jokertecken.
* **Anslutningssäkerhet**: säkerställer att alla data som överförs mellan IoT-enhet och IoT-hubben är konfidentiellt och förfalska.
* **Cloud Security**: ge möjligheter att säkra data medan den går genom och lagras i molnet.

![Tre säkerhetsområden][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Säker enhetsetableringen och autentisering

IoT-Lösningsacceleratorer säker IoT-enheter med hjälp av följande två metoder:

* Genom att tillhandahålla en unik identitetsnyckel (säkerhetstoken) för varje enhet som kan användas av enheten kommunicerar med IoT-hubben.
* Med hjälp av en på enhet [X.509-certifikat] [ lnk-x509] och den privata nyckeln som ett sätt att autentisera enheten till IoT-hubben. Den här autentiseringsmetoden garanterar att den privata nyckeln på enheten inte är känd utanför enheten när som helst, vilket ger en högre säkerhetsnivå.

Token säkerhetsmetod ger autentisering för varje anrop som görs av enheten IoT-hubb genom att associera den symmetriska nyckeln till varje anrop. X.509-baserad autentisering tillåter autentisering för en IoT-enhet på det fysiska skiktet som en del av TLS-anslutningen upprättas. Säkerhets-token-baserade-metoden kan användas utan X.509-autentisering, vilket är ett mindre säkert mönster. Valet mellan de två metoderna beror i första hand hur säker enhet autentisering måste vara och tillgänglighet för säker lagring på enheten (för att lagra den privata nyckeln säkert).

## <a name="iot-hub-security-tokens"></a>Säkerhetstoken i IoT Hub

IoT-hubb använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. Dessutom är säkerhetstoken begränsad giltighetstid och omfång. Azure IoT-SDK: er generera automatiskt token utan någon specialkonfiguration. Vissa scenarier, men användaren att skapa och använda säkerhetstoken direkt. Dessa scenarier som inkluderar direkt användning av de MQTT, AMQP och HTTP- eller implementering av tokentjänsten mönstret.

Mer information om strukturen för säkerhetstoken och användning finns i följande artiklar:

* [Säkerhet token struktur][lnk-security-tokens]
* [Med hjälp av SAS-token som en enhet][lnk-sas-tokens]

Varje IoT-hubben har en [identitetsregistret] [ lnk-identity-registry] som kan användas för att skapa per enhet resurser i tjänsten, till exempel en kö som innehåller relä moln till enhet meddelanden och att tillåta åtkomst till enheten riktade slutpunkter. IoT-hubb identitetsregistret ger säker lagring av enheten identiteter och säkerhetsnycklar för en lösning. En eller flera enheter identiteter kan läggas till en lista över tillåtna eller blockerade webbplatser, aktivera fullständig kontroll över enheten. I följande artiklar innehåller mer information om strukturen i identitetsregistret och de åtgärder som stöds.

[IoT-hubb stöder till exempel MQTT, AMQP och HTTP-protokoll][lnk-protocols]. Var och en av dessa protokoll använder säkerhetstoken från IoT-enhet till IoT-hubb på olika sätt:

* AMQP: SASL vanlig AMQP anspråksbaserad säkerhet och (`{policyName}@sas.root.{iothubName}` med IoT-hubb-nivå token; `{deviceId}` med enheten omfång token).
* MQTT: Ansluta paket använder `{deviceId}` som den `{ClientId}`, `{IoThubhostname}/{deviceId}` i den **användarnamn** fältet och en SAS-token i den **lösenord** fältet.
* HTTP: Är giltig token i auktoriseringshuvudet för begäran.

IoT-hubb identitetsregistret kan användas för att konfigurera per enhet säkerhetsreferenser och åtkomstkontroll. Men om en IoT-lösningen har redan en betydande investering i en [anpassade identitet registret och/eller autentisering schemat][lnk-custom-auth], kan integreras i en befintlig infrastruktur med IoT-hubben genom att skapa en token tjänst.

### <a name="x509-certificate-based-device-authentication"></a>X.509 certifikatbaserad autentisering

Användning av en [enhetsbaserad X.509-certifikat] [ lnk-use-x509] och dess associerade privata och offentliga nyckelpar tillåter ytterligare autentisering på det fysiska skiktet. Den privata nyckeln lagras på ett säkert sätt i enheten och är inte kan upptäckas utanför enheten. X.509-certifikat innehåller information om enhet, till exempel enhets-ID och andra organisatoriska information. En signatur för certifikatet skapas med hjälp av den privata nyckeln.

Övergripande enheten etablering flöde:

* Associera en identifierare för en fysisk enhet – enhetsidentitet och/eller X.509-certifikat som är kopplade till enheten under enhet tillverkar eller idriftsättning.
* Skapa en motsvarande identity-transaktion i IoT-hubb – enhetsidentitet och associerade enhetsinformation i IoT-hubb identitetsregistret.
* Tumavtrycket för X.509-certifikatet lagras säkert i IoT-hubb identitetsregistret.

### <a name="root-certificate-on-device"></a>Rotcertifikatet på enheter

När du upprättar en säker TLS-anslutning med IoT-hubben autentiserar IoT-enhet IoT-hubb med ett rotcertifikat som är en del av enheten SDK. Certifikatet för klientens C SDK finns under mappen ”\\c\\certifikat” under roten av lagringsplatsen. Även om dessa rotcertifikat är långlivade, kan de fortfarande eller upphöra att gälla. Om det inte går att uppdatera certifikatet på enheten, kan enheten inte därefter ansluta till IoT-hubb (eller någon annan molntjänst). Med ett sätt att uppdatera rotcertifikatet när IoT-enhet har distribuerats effektivt minskar risken.

## <a name="securing-the-connection"></a>Att säkra anslutningen

Internet-anslutning mellan IoT-enhet och IoT-hubb skyddas med Transport Layer Security (TLS)-standard. Azure IoT stöder [TLS 1.2][lnk-tls12], TLS 1.1 och TLS 1.0 i den här ordningen. Stöd för TLS 1.0 tillhandahålls endast för bakåtkompatibilitet. Använd om möjligt TLS 1.2 eftersom det ger bäst skydd.

## <a name="securing-the-cloud"></a>Skydda molnet

Azure IoT-hubb tillåter definition av [åtkomstprinciper kontrollen] [ lnk-protocols] för varje säkerhetsnyckel. Följande uppsättning behörigheter som används för att bevilja åtkomst till var och en av IoT-hubb slutpunkter. Behörigheter som begränsar åtkomsten till en IoT-hubb som baseras på funktionen.

* **RegistryRead**. Ger läsbehörighet till identitetsregistret. Mer information finns i [identitetsregistret][lnk-identity-registry].
* **RegistryReadWrite**. Ger Läs- och skrivåtkomst till identitetsregistret. Mer information finns i [identitetsregistret][lnk-identity-registry].
* **ServiceConnect**. Ger åtkomst till cloud service-kommunikation och övervakning av slutpunkter. Till exempel ger den behörighet till backend-molntjänster att ta emot meddelanden från enhet till moln, skicka meddelanden moln till enhet och hämta motsvarande leverans bekräftelser.
* **DeviceConnect**. Ger åtkomst till enheten riktade slutpunkter. Det ger till exempel behörighet att skicka meddelanden från enhet till moln och ta emot meddelanden moln till enhet. Den här behörigheten används av enheter.

Det finns två sätt att hämta **DeviceConnect** behörigheter med IoT-hubb med [säkerhetstoken][lnk-sas-tokens]: med identitetsnyckel en enhet eller en nyckel för delad åtkomst. Dessutom är det viktigt att Observera att alla funktioner som är tillgängliga från enheter exponeras avsiktligt på slutpunkter med prefixet `/devices/{deviceId}`.

[Tjänstens komponenter kan bara generera säkerhetstoken] [ lnk-service-tokens] använder delad åtkomstprinciper som beviljar behörighet.

Azure IoT-hubb och andra tjänster som kan vara en del av lösningen kan hantering av användare med Azure Active Directory.

Data som inhämtas av Azure IoT-hubb kan användas av en mängd olika tjänster, till exempel Azure Stream Analytics och Azure-blobblagring. Dessa tjänster tillåta åtkomst. Läs mer om dessa tjänster och tillgängliga alternativ:

* [Azure Cosmos-DB][lnk-cosmosdb]: en skalbar, -indexerat databastjänst för halvstrukturerade data som hanterar metadata för enheterna som du etablerar, till exempel attribut, konfiguration och egenskaper för säkerhetsbehörighet. Azure Cosmos-DB ger hög prestanda och hög genomströmning bearbetning, schema-oberoende indexering av data och en omfattande SQL-gränssnitt.
* [Azure Stream Analytics][lnk-asa]: realtid dataströmmen som bearbetas i molnet som gör att du kan snabbt utveckla och distribuera en billig analytics lösning för att upptäcka realtidsinsikter från enheter, sensorer, infrastruktur, och program. Data från den här fullständigt hanterade tjänsten kan skalas till någon volym och ändå upprätthålla högt genomflöde, låg latens och återhämtning.
* [Azure Apptjänst][lnk-appservices]: en molnplattform för att skapa kraftfulla webb- och mobilappar som ansluter till data var som helst; i molnet eller lokalt. Skapa spännande mobilappar för iOS, Android och Windows. Integrera med programvara som en tjänst (SaaS) och företagsprogram med out box anslutningen till massor av molnbaserade tjänster och företagsprogram. Kod i dina Favoriter språk och IDE (.NET, Node.js, PHP, Python eller Java) att skapa webbprogram och API: er snabbare än någonsin.
* [Logic Apps][lnk-logicapps]: funktion i Logic Apps i Azure App Service hjälper till att integrera IoT-lösningen till din befintliga line-of-business-system och automatisera arbetsflödesprocesser. Logic Apps kan utvecklare utforma arbetsflöden som startar från en utlösare och sedan exekverar en följd av steg, regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärsprocesser. Logic Apps erbjuder out box-anslutning till ett brett spektrum av SaaS, molnbaserade, och lokala program.
* [Azure-blobblagring][lnk-blob]: tillförlitliga ekonomiska molnlagring för de data som dina enheter skicka till molnet.

## <a name="conclusion"></a>Sammanfattning

Den här artikeln innehåller översikt över implementering information för att utforma och distribuera en IoT-infrastruktur med hjälp av Azure IoT. Nyckeln i att skydda den övergripande IoT-infrastrukturen är att konfigurera varje komponent säker. Designalternativ som är tillgängliga i Azure IoT tillhandahåller en viss nivå flexibilitet och val. varje alternativ kan dock ha säkerhetsaspekter. Du rekommenderas att var och en av dessa alternativ att utvärderas via en utvärdering av risk/kostnad.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-cosmosdb]: https://azure.microsoft.com/services/cosmos-db/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
