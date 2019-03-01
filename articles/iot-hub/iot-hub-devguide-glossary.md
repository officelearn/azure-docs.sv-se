---
title: Azure IoT Hub i ordlista | Microsoft Docs
description: Utvecklarguide – en ordlista med vanliga termer som är relaterade till Azure IoT Hub.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: d79485fa7587a6cf8783f2e5779fa0a1d593aea7
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011440"
---
# <a name="glossary-of-iot-hub-terms"></a>IoT Hub-ordlista
Den här artikeln innehåller några av de vanliga termer som används i IoT Hub-artiklar.

## <a name="advanced-message-queueing-protocol"></a>Avancerade Message Queueing Protocol
[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) är en av meddelandetjänsterna protokoll som [IoT Hub](#iot-hub) har stöd för att kommunicera med enheter. Mer information om meddelandeprotokoll som stöds av IoT Hub finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatisk enhetshantering
Automatisk hantering av enheter i Azure IoT Hub automatiserar repetitiva och komplexa uppgifter för att hantera stora enheten fjärranläggning över hela deras livscykler. Med automatisk hantering av enheter du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de träder i omfånget.  Består av [automatisk enhetskonfigurationer](iot-hub-auto-device-config.md) och [automatisk IoT Edge-distributioner](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Automatisk enhetskonfiguration
Lösningens serverdel kan använda [automatisk enhetskonfigurationer](iot-hub-auto-device-config.md) tilldela önskade egenskaper till en uppsättning [enhetstvillingar](#device-twin) och rapportera status med hjälp av systemmått och anpassade mått. 

## <a name="azure-classic-cli"></a>Klassisk Azure CLI
Den [Azure klassiskt CLI](../cli-install-nodejs.md) är ett kommandoradsverktyg för flera plattformar, öppen källkod, shell-baserade, för att skapa och hantera resurser i Microsoft Azure. Den här versionen av CLI ska användas för endast klassiska distributioner.

## <a name="azure-cli"></a>Azure CLI
Den [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) är ett kommandoradsverktyg för flera plattformar, öppen källkod, shell-baserade, för att skapa och hantera resurser i Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>Azure SDK: er för IoT-enheter
Det finns _SDK: er enheter_ lagringsklientbibliotek för flera språk som gör det möjligt att skapa [enhetsappar](#device-app) som interagerar med en IoT-hubb. IoT Hub-självstudier visar hur du använder SDK: er dessa enheter. Du hittar källkoden och ytterligare information om SDK: er för enheter i den här GitHub [databasen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDK: er
Det finns _service SDK: er_ lagringsklientbibliotek för flera språk som gör det möjligt att skapa [appar för serverdelar](#back-end-app) som interagerar med en IoT-hubb. IoT Hub-självstudier visar hur du använder dessa tjänst-SDK: er. Du hittar källkoden och ytterligare information om tjänst-SDK: er i den här GitHub [databasen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT-verktyg
Den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett plattformsoberoende, öppen källkod Visual Studio Code-tillägg som hjälper dig att hantera Azure IoT Hub och enheter i VS Code. IoT-utvecklare kan utveckla IoT-projekt i VS Code enkelt med Azure IoT-verktyg.

## <a name="azure-portal"></a>Azure Portal
Den [Microsoft Azure-portalen](https://portal.azure.com) är en central plats där du kan etablera och hantera Azure-resurser. Den organiserar dess innehåll med hjälp av _blad_.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) är en uppsättning cmdletar som du kan använda för att hantera Azure med Windows PowerShell. Du kan använda cmdletar för att skapa, testa, distribuera och hantera lösningar och tjänster som levererats via Azure-plattformen.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i din lösning som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) tillhandahåller molnbaserad kommunikation med företagsmeddelandetjänster och vidarebefordrande kommunikation som hjälper dig att ansluta dina lokala lösningar till molnet. Vissa IoT Hub-självstudier gör använder Service Bus [köer](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md) är en lösning för molnlagring. Den innehåller Blob Storage-tjänsten som du kan använda för att lagra Ostrukturerade objektdata. Vissa IoT Hub-självstudier använder blob storage.

## <a name="back-end-app"></a>Backend-app
I samband med [IoT Hub](#iot-hub), en backend-app är en app som ansluter till en tjänst-slutpunkter i IoT hub. Till exempel en backend-app kan hämta [enhet till moln](#device-to-cloud) meddelanden eller hantera den [identitetsregistret](#identity-registry). Vanligtvis en backend-app som körs i molnet, men i många av självstudierna backend-apparna är konsolappar som körs på din lokala utvecklingsdator.

## <a name="built-in-endpoints"></a>Inbyggda slutpunkter
Varje IoT-hubb innehåller en inbyggd [endpoint](iot-hub-devguide-endpoints.md) dvs Event Hub-kompatibla. Du kan använda valfri metod som fungerar med Event Hubs kan läsa meddelanden från enhet till moln från den här slutpunkten.

## <a name="cloud-gateway"></a>Molngatewayen
En molngateway möjliggör anslutningar för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). En molngateway ligger i molnet jämfört med en [fältgateway](#field-gateway) som körs lokalt till dina enheter. Ett vanligt användningsfall för en molngateway är att implementera översättning av protokollet för dina enheter.

## <a name="cloud-to-device"></a>Moln till enhet
Refererar till meddelanden som skickas från en IoT hub till en ansluten enhet. Dessa meddelanden är ofta kommandon som instruerar enheten att vidta åtgärder. Mer information finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguration
I samband med [automatisk enhetskonfiguration](iot-hub-auto-device-config.md), en konfiguration i IoT Hub definierar den önskade konfigurationen för en uppsättning enheter twins och innehåller en uppsättning av mått för att rapportera status och förlopp.

## <a name="connection-string"></a>Anslutningssträng
Du kan använda anslutningssträngar i din kod för att kapsla in informationen som krävs för att ansluta till en slutpunkt. En anslutningssträng innehåller normalt sett adressen för slutpunkten och säkerhetsinformation, men anslutningssträngen format varierar mellan olika tjänster. Det finns två typer av anslutningssträng som är associerad med tjänsten IoT Hub:
- *Anslutningssträngar för enheten* att enheterna ska ansluta till enheten-slutpunkter i IoT hub.
- *IoT Hub-anslutningssträngar* aktivera backend-appar att ansluta till tjänsten-slutpunkter i IoT hub.

## <a name="custom-endpoints"></a>Anpassade slutpunkter
Du kan skapa anpassade [slutpunkter](iot-hub-devguide-endpoints.md) på en IoT-hubb för att leverera meddelanden som skickas av en [routningsregel](#routing-rules). Anpassade slutpunkter ansluta direkt till en händelsehubb, en Service Bus-kö eller ett Service Bus-ämne.

## <a name="custom-gateway"></a>Anpassade gateway
Möjliggör en gateway-anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Du kan använda Azure IoT Edge kan skapa anpassade gateways som implementerar anpassad logik för att hantera meddelanden, anpassade protokoll konverteringar och annan bearbetning på gränsen.

## <a name="data-point-message"></a>Datapunkt meddelande
En datapunkt meddelandet är ett [enhet till moln](#device-to-cloud) meddelande som innehåller [telemetri](#telemetry) data, till exempel vindhastighet eller temperatur.

## <a name="desired-configuration"></a>Önskad konfiguration
I samband med en [enhetstvillingen](iot-hub-devguide-device-twins.md), desired configuration refererar till en fullständig uppsättning egenskaper och metadata i enhetstvillingen som ska synkroniseras med enheten.

## <a name="desired-properties"></a>Önskade egenskaper
I samband med en [enhetstvillingen](iot-hub-devguide-device-twins.md), önskade egenskaper är ett underavsnitt enhetstvillingens som används med [rapporterade egenskaper](#reported-properties) att synkronisera enhetskonfiguration eller ett villkor. Önskade egenskaper kan bara ställas in en [serverdelsappen](#back-end-app) och observerats av den [enhetsapp](#device-app).

## <a name="device-to-cloud"></a>Enhet till moln
Refererar till meddelanden som skickas från en ansluten enhet till [IoT Hub](#iot-hub). Dessa meddelanden kan vara [datapunkt](#data-point-message) eller [interaktiva](#interactive-message) meddelanden. Mer information finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Enhet
En enhet är vanligtvis en småskalig, fristående datorenhet som kan samla in data eller andra enheter i kontexten för IoT. Till exempel kan en enhet vara en miljömässig övervakning enhet eller en domänkontrollant för vatten och ventilation system i en växthusgaser. Den [enhetskatalog](https://catalog.azureiotsolutions.com/) innehåller en lista över maskinvaruenheter som certifierats för att fungera med [IoT Hub](#iot-hub).

## <a name="device-app"></a>App för enheter
En app för enheter som körs på din [enhet](#device) och hanterar kommunikationen med din [IoT-hubb](#iot-hub). Normalt använder du någon av de [SDK: er för Azure IoT-enheter](#azure-iot-device-sdks) när du implementerar en enhetsapp. I många av IoT-självstudier, använder du en [simulerad enhet](#simulated-device) i informationssyfte.

## <a name="device-condition"></a>Villkor för enhet
Refererar till information om enhetstillstånd, t.ex anslutningsmetod som används, som rapporterats av en [enhetsapp](#device-app). [Enhetsappar](#device-app) även rapportera deras funktioner. Du kan fråga efter information om villkor och funktionen med hjälp av enhetstvillingar.

## <a name="device-data"></a>Enhetsdata
Enhetsdata refererar till de enhetsspecifika data som lagras i IoT Hub [identitetsregistret](#identity-registry). Det är möjligt att importera och exportera dessa data.

## <a name="device-explorer"></a>Enhetsutforskaren
Den [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) är ett verktyg som körs på Windows och hjälper dig att hantera dina enheter i den [identitetsregistret](#identity-registry). Verktyget kan också skicka och ta emot meddelanden till dina enheter.

## <a name="device-identity"></a>Enhetsidentitet
Enhetens identitet är den unika identifieraren som tilldelats varje enhet har registrerats i den [identitetsregistret](#identity-registry).

## <a name="device-management"></a>Enhetshantering
Enhetshantering omfattar hela livscykeln som är associerade med hantering av enheter i din IoT-lösning, inklusive planering, etablera, konfigurera, övervakning och tas ur bruk.

## <a name="device-management-patterns"></a>Enhetshanteringsmönster
[IoT hub](#iot-hub) möjliggör vanliga enhetshanteringsmönster som omstart, utföra fabriksåterställning och utför uppdateringar av inbyggd programvara på dina enheter.

## <a name="device-rest-api"></a>REST API för enhet
Du kan använda den [enheten REST API](https://docs.microsoft.com/rest/api/iothub/device) från en enhet för att skicka meddelanden från enheten till molnet till en IoT hub och ta emot [moln till enhet](#cloud-to-device) meddelanden från en IoT-hubb. Normalt bör du använda en av de högre nivå [SDK: er enheter](#azure-iot-device-sdks) som visas i IoT Hub-självstudier.

## <a name="device-provisioning"></a>Enhetsetablering
Enhetsetablering är processen att lägga till första [enhetsdata](#device-data) till datalager i din lösning. Om du vill aktivera en ny enhet att ansluta till din hubb, måste du lägga till ett enhets-ID och nycklar till IoT Hub [identitetsregistret](#identity-registry). Som en del av etableringen, kan du behöva initiera enhetsspecifika data i andra lösningen butiker.

## <a name="device-twin"></a>Enhetstvilling
En [enhetstvillingen](iot-hub-devguide-device-twins.md) är JSON-dokument som lagrar information om enhetstillstånd, till exempel metadata, konfigurationer och villkor. [IoT Hub](#iot-hub) lagrar en enhetstvilling för varje enhet som du etablerar i IoT hub. Enhetstvillingar gör det möjligt att synkronisera [enheten villkor](#device-condition) och serverdelen för konfigurationer mellan enheten och lösningen. Du kan fråga efter enhetstvillingar för att hitta specifika enheter och fråga efter statusen för långvariga åtgärder.

## <a name="direct-method"></a>Direktmetod
En [direktmetod](iot-hub-devguide-direct-methods.md) är ett sätt som du kan utlösa en metod för att köra på en enhet genom att aktivera en API på IoT hub.

## <a name="endpoint"></a>Slutpunkt
En IoT hub exponerar flera [slutpunkter](iot-hub-devguide-endpoints.md) som gör att dina appar att ansluta till IoT hub. Det finns enheter-slutpunkter som gör att enheter att utföra åtgärder som att skicka [enhet till moln](#device-to-cloud) meddelanden och som tar emot [moln till enhet](#cloud-to-device) meddelanden. Det finns service-av hanteringsslutpunkter som gör att [backend-appar](#back-end-app) att utföra åtgärder som [enhetsidentitet](#device-identity) hanterings- och twin enhetshantering. Finns på service-riktade [inbyggda slutpunkter](#built-in-endpoints) för att läsa meddelanden från enheten till molnet. Du kan skapa [anpassade slutpunkter](#custom-endpoints) att ta emot enhet-till-moln-meddelanden som skickas av en [routningsregel](#routing-rules).

## <a name="event-hubs-service"></a>Event Hubs-tjänsten
[Händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund. Tjänsten kan du bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. En jämförelse med tjänsten IoT Hub i [jämförelse av Azure IoT Hub och Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Händelsehubb-kompatibel slutpunkt
Att läsa [enhet till moln](#device-to-cloud) meddelandena som skickas till din IoT-hubb, du kan ansluta till en slutpunkt på din hubb och använda alla Event Hubs-kompatibla-metoden för att läsa dessa meddelanden. Event Hubs-kompatibla sätt är att med hjälp av den [SDK: er för Event Hubs](../event-hubs/event-hubs-programming-guide.md) och [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Fält-gateway
Möjliggör en fältgateway-anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub) och vanligtvis har distribuerats lokalt med dina enheter. Mer information finns i [vad är Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Kostnadsfritt konto
Du kan skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/) slutförts IoT Hub-självstudier och experimentera med IoT Hub-tjänsten (och andra Azure-tjänster).

## <a name="gateway"></a>Gateway
Möjliggör en gateway-anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Se även [fältet Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway), och [anpassade Gateway](#custom-gateway).

## <a name="identity-registry"></a>Identitetsregistret
Den [identitetsregistret](iot-hub-devguide-identity-registry.md) är den inbyggda komponenten i en IoT-hubb som lagrar information om enskilda enheter som tillåts att ansluta till en IoT-hubb.

## <a name="interactive-message"></a>Interaktiv meddelande
En interaktiv meddelandet är ett [moln till enhet](#cloud-to-device) meddelanden som utlöser en omedelbar åtgärd i lösningens serverdel. En enhet kan till exempel skicka ett larm om ett fel som automatiskt ska loggas en CRM-systemet.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT Hub är en fullständigt hanterad Azure-tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. Mer information finns i [vad är Azure IoT Hub?](about-iot-hub.md) Med hjälp av din [Azure-prenumeration](#subscription), du kan skapa IoT-hubbar för att hantera dina IoT-meddelanden arbetsbelastningar.

## <a name="iot-hub-metrics"></a>IoT Hub metrics
[IoT Hub mått](iot-hub-metrics.md) ger dig information om tillstånd för IoT-hubbar i din [Azure-prenumeration](#subscription). IoT Hub mått kan du utvärdera den övergripande hälsan för tjänsten och de enheter som är anslutna till den. IoT Hub mått kan du se vad som händer med din IoT-hubb och undersöka grundorsaken problem utan att behöva kontakta Azure-supporten.

## <a name="iot-hub-query-language"></a>IoT Hub-frågespråk
Den [IoT Hub-frågespråk](iot-hub-devguide-query-language.md) är ett SQL-liknande språk som gör att du kan fråga din [ ](#job) och enhetstvillingar.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub-resurs REST-API
Du kan använda den [IoT Hub Resource REST API: et](https://docs.microsoft.com/rest/api/iothub/iothubresource) att hantera IoT-hubbar i din [Azure-prenumeration](#subscription) utför åtgärder som att skapa, uppdatera och ta bort hubs.

## <a name="iot-solution-accelerators"></a>IoT-lösningsacceleratorer
Azure IoT-Lösningsacceleratorer paketera samman flera Azure-tjänster till lösningar. Dessa lösningar kan du snabbt komma igång med slutpunkt till slutpunkt-implementeringar av vanliga IoT-scenarier. Mer information finns i [vad är Azure IoT-Lösningsacceleratorer?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>IoT-tillägget för Azure CLI 
[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är en plattformsoberoende, kommandorads-verktyget. Verktyget gör det möjligt att hantera enheter i den [identitetsregistret](#identity-registry), skicka och ta emot meddelanden och filer från dina enheter och övervaka din IoT hub-åtgärder.

## <a name="job"></a>Jobb
Lösningens serverdel kan använda [jobb](iot-hub-devguide-jobs.md) att schemalägga och spåra aktiviteter i en uppsättning enheter som registrerats med IoT-hubben. Aktiviteter är till exempel uppdatera enhetstvillingen [önskade egenskaper](#desired-properties), uppdatera enhetstvillingen [taggar](#tags), och anropa [direkta metoder](#direct-method). [IoT Hub](#iot-hub) använder också att [att importera och exportera](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) från den [identitetsregistret](#identity-registry).

## <a name="modules"></a>Moduler
På enheten, SDK: er för IoT Hub-enheter kan du skapa [moduler](iot-hub-devguide-module-twins.md) där var och en öppnar en oberoende anslutning till IoT Hub. Den här funktionen kan du använda separata namnområden för olika komponenter på din enhet.

Modulen identitets- och modultvilling innehåller samma funktioner som [enhetsidentitet](#device-identity) och [enhetstvillingen](#device-twin) men med en bättre precision. Den här finare granularitet gör det möjligt för kompatibla enheter, till exempel operativsystem-baserade enheter eller firmware-enheter som hanterar flera komponenter, om du vill isolera konfiguration och villkor för var och en av dessa komponenter.

## <a name="module-identity"></a>Modulen identitet
Modulen identiteten är den unika identifieraren som tilldelats varje modul som hör till en enhet. Modulen identitet även är registrerat i den [identitetsregistret](#identity-registry).

## <a name="module-twin"></a>Modultvilling
Liknar enhetstvillingen, en modultvilling är JSON-dokument som lagrar information om modulen tillstånd som metadata, konfigurationer och villkor. IoT Hub lagrar en modultvillingen för varje modul-identitet som du etablerar under en enhetsidentitet i IoT hub. Modultvillingar kan du synkronisera modulen villkor och konfigurationer mellan modulen och lösningens backend-server. Du kan fråga modultvillingar för att hitta specifika moduler och fråga efter statusen för långvariga åtgärder.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) är en av meddelandetjänsterna protokoll som [IoT Hub](#iot-hub) har stöd för att kommunicera med enheter. Mer information om meddelandeprotokoll som stöds av IoT Hub finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Övervakning av åtgärder
IoT Hub [åtgärdsövervakning](iot-hub-operations-monitoring.md) kan du övervaka statusen för åtgärder på IoT hub i realtid. [IoT Hub](#iot-hub) spårar händelser över flera åtgärdskategorier. Du kan välja skicka händelser från en eller flera kategorier till en IoT Hub-slutpunkt för bearbetning. Du kan övervaka data för fel eller skapa mer komplexa bearbetning baserad på datamönster.

## <a name="physical-device"></a>Fysisk enhet
En fysisk enhet är en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb. För enkelhetens skull använder många av IoT Hub-självstudier [simulerade enheter](#simulated-device) så att du kan köra exempel på den lokala datorn.

## <a name="primary-and-secondary-keys"></a>Primära och sekundära nycklar
När du ansluter till en enhet webbservergrupper eller service-riktade slutpunkt på en IoT-hubb din [anslutningssträngen](#connection-string) innehåller nyckel för att bevilja dig åtkomst. När du lägger till en enhet på den [identitetsregistret](#identity-registry) eller lägga till en [delad åtkomstprincip](#shared-access-policy) till hubben, tjänsten genererar en primär och sekundär nyckel. Eftersom det finns två nycklar kan du förnyar från en nyckel till en annan när du uppdaterar en nyckel utan att förlora åtkomst till IoT hub.

## <a name="protocol-gateway"></a>Protokoll-gateway
En protokoll-gateway distribueras normalt i molnet och tillhandahåller protokoll översättning för enheter som ansluter till [IoT Hub](#iot-hub). Mer information finns i [vad är Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar
Det finns olika [kvoter](iot-hub-devguide-quotas-throttling.md) som gäller för din användning av [IoT Hub](#iot-hub), många av kvoter kan variera beroende på vilken nivå av IoT hub. [IoT Hub](#iot-hub) gäller även [begränsar](iot-hub-devguide-quotas-throttling.md) för din användning av tjänsten vid körning.

## <a name="reported-configuration"></a>Rapporterade konfiguration
I samband med en [enhetstvillingen](iot-hub-devguide-device-twins.md), rapporterade konfiguration refererar till en fullständig uppsättning egenskaper och metadata i enhetstvillingen som ska rapporteras till lösningens backend-server.

## <a name="reported-properties"></a>Rapporterade egenskaper
I samband med en [enhetstvillingen](iot-hub-devguide-device-twins.md), rapporterade egenskaper är ett underavsnitt enhetstvillingens används med [önskade egenskaper](#desired-properties) att synkronisera enhetskonfiguration eller ett villkor. Rapporterade egenskaper kan bara ställas in den [enhetsapp](#device-app) och kan läsas och med en [serverdelsappen](#back-end-app).

## <a name="resource-group"></a>Resursgrupp
[Azure Resource Manager](#azure-resource-manager) använder resursgrupper för att gruppera relaterade resurser. Du kan använda en resursgrupp för att utföra åtgärder på alla resurser i den aktuella samtidigt.

## <a name="retry-policy"></a>Återförsöksprincip
Du hanterar med hjälp av en återförsöksprincip [tillfälliga fel](/azure/architecture/best-practices/transient-faults) när du ansluter till en tjänst i molnet.

## <a name="routing-rules"></a>Hanteringsregler
Du konfigurerar [routningsregler](iot-hub-devguide-messages-read-custom.md) i IoT-hubben att routa enhet-till-moln-meddelanden till en [inbyggd slutpunkt](#built-in-endpoints) eller [anpassade slutpunkter](#custom-endpoints) för bearbetning av lösningens backend-servrar .

## <a name="sasl-plain"></a>SASL OFORMATERAD
SASL OFORMATERAD är ett protokoll som AMQP-protokollet som används för att överföra säkerhetstoken.

## <a name="service-rest-api"></a>Tjänsten REST API
Du kan använda den [Service REST API](https://docs.microsoft.com/rest/api/iothub/service) från lösningen serverdel för att hantera dina enheter. API: et kan du hämta och uppdatera [enhetstvillingen](#device-twin) egenskaper, anropa [direkta metoder](#direct-method), och schema [jobb](#job). Normalt bör du använda en av de högre nivå [service SDK: er](#azure-iot-service-sdks) som visas i IoT Hub-självstudier.

## <a name="shared-access-signature"></a>Signatur för delad åtkomst
Signaturer för delad åtkomst (SAS) är en autentiseringsmetod baserad på säkra SHA-256-hashvärden eller URI: er. SAS-autentisering har två komponenter: en _Policy för delad åtkomst_ och en _signatur för delad åtkomst_ (kallas ofta en token). En enhet använder SAS för att autentisera med en IoT-hubb. [Appar för serverdelar](#back-end-app) också använda SAS för att autentisera med service-slutpunkter i IoT hub. Normalt kan du inkludera det SAS-token i den [anslutningssträngen](#connection-string) att en app använder för att upprätta en anslutning till en IoT-hubb.

## <a name="shared-access-policy"></a>Policy för delad åtkomst
En princip för delad åtkomst definierar behörigheterna för alla som har en giltig [primära eller sekundära nyckeln](#primary-and-secondary-keys) som är associerade med principen. Du kan hantera principer för delad åtkomst och nycklar för hubben i den [portal](#azure-portal).

## <a name="simulated-device"></a>Simulerad enhet
För enkelhetens skull använder många av IoT Hub-självstudier simulerade enheter så att du kan köra exempel på den lokala datorn. Däremot ett [fysisk enhet](#physical-device) är en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb.

## <a name="solution"></a>Lösning
En _lösning_ kan referera till ett Visual Studio-lösning som innehåller ett eller flera projekt. En _lösning_ kan också referera till en IoT-lösning som innehåller element, till exempel enheter, [enhetsappar](#device-app), en IoT-hubb, andra Azure-tjänster och [appar för serverdelar](#back-end-app).

## <a name="subscription"></a>Prenumeration
En Azure-prenumeration är om fakturering sker. Varje Azure-resurs som du skapar eller Azure service som du använder är associerade med en enda prenumeration. Det gäller även många kvoter på nivån för en prenumeration.

## <a name="system-properties"></a>Systemegenskaper
I samband med en [enhetstvillingen](iot-hub-devguide-device-twins.md), Systemegenskaper är skrivskyddade och inkludera information om enhetsanvändning, till exempel senaste aktivitet tid och anslutningen tillstånd.

## <a name="tags"></a>Taggar
I samband med en [enhetstvillingen](iot-hub-devguide-device-twins.md), taggar är enhetsmetadata lagras och hämtas av serverdelen i lösningen i form av ett JSON-dokument. Taggar visas inte för appar på en enhet.

## <a name="telemetry"></a>Telemetri
Enheter samla in telemetridata, till exempel vindhastighet eller temperatur, och använder datapunkt meddelanden för att skicka telemetri till en IoT-hubb.

## <a name="token-service"></a>Token-tjänsten
Du kan använda en token tjänst för att implementera en autentiseringsmekanism för dina enheter. Den använder en IoT-hubb [delad åtkomstprincip](#shared-access-policy) med **DeviceConnect** behörighet att skapa *enhet-omfattande* token. Dessa token kan en enhet ansluter till IoT hub. En enhet använder en anpassad autentiseringsmetod för att autentisera med tjänsten token. Om enheten autentiserar har, utfärdar tokentjänsten som en SAS-token för enheten du använder för att få åtkomst till din IoT-hubb.

## <a name="twin-queries"></a>Twin frågor
[Enheten och modulen twin frågor](iot-hub-devguide-query-language.md) använder SQL-liknande IoT Hub-frågespråk för att hämta information från din enhetstvillingar eller modultvillingar. Du kan använda samma IoT Hub-frågespråk för att hämta information om [](#job) som körs i din IoT-hubb.

## <a name="twin-synchronization"></a>Twin synkronisering
Twin synkronisering använder den [önskade egenskaper](#desired-properties) i enhetstvillingar eller modultvillingar för att konfigurera dina enheter eller moduler och hämta [rapporterade egenskaper](#reported-properties) från att lagra i läsningen.

## <a name="x509-client-certificate"></a>X.509-klientcertifikatet
En enhet kan använda ett X.509-certifikat för att autentisera med [IoT Hub](#iot-hub). Med hjälp av ett X.509-certifikat är ett alternativ till att använda en [SAS-token](#shared-access-signature).
