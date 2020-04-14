---
title: Azure IoT Hub ordlista med termer | Microsoft-dokument
description: Utvecklarguide – en ordlista som förklarar några av de vanliga termer som används i Azure IoT Hub-artiklarna.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 0c4a31d6273a7d440340fc1f907f287201134244
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258413"
---
# <a name="glossary-of-iot-hub-terms"></a>Ordlista med IoT Hub-termer

I den här artikeln visas några av de vanliga termer som används i IoT Hub-artiklarna.

## <a name="advanced-message-queueing-protocol"></a>Protokoll för avancerad meddelandeköer

[AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) är ett av de meddelandeprotokoll som [IoT Hub](#iot-hub) stöder för kommunikation med enheter. Mer information om meddelandeprotokollen som IoT Hub stöder finns i [Skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatisk enhetshantering

Automatisk enhetshantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna att hantera stora enhetsflottor under hela deras livscykler. Med Automatisk enhetshantering kan du rikta in dig på en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de kommer in i omfånget.  Består av [automatiska enhetskonfigurationer](iot-hub-auto-device-config.md) och [IoT Edge automatiska distributioner](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Automatisk enhetskonfiguration

Lösningens serverdel kan använda [automatiska enhetskonfigurationer](iot-hub-auto-device-config.md) för att tilldela önskade egenskaper till en uppsättning [enhetstvillingar](#device-twin) och rapportstatus med hjälp av systemmått och anpassade mått. 

## <a name="azure-classic-cli"></a>Klassisk Azure CLI

[Azure classic CLI](../cli-install-nodejs.md) är ett plattformsoberoende kommandoverktyg med öppen källkod med öppen källkod för att skapa och hantera resurser i Microsoft Azure. Den här versionen av CLI bör endast användas för klassiska distributioner.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) är ett plattformsoberoende kommandoverktyg med öppen källkod med öppen källkod för att skapa och hantera resurser i Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>Azure IoT-enhet SDK:er

Det finns _enhets-SDK:er_ tillgängliga för flera språk som gör att du kan skapa [enhetsappar](#device-app) som interagerar med en IoT-hubb. IoT Hub-självstudierna visar hur du använder dessa enhets-SDK:er. Du hittar källkoden och ytterligare information om enhetens SDK:er i den här [GitHub-databasen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-explorer"></a>Utforskare för Azure IoT

[Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) används för att visa telemetri som enheten skickar, arbetar med enhetsegenskaper och anropskommandon. Du kan också använda utforskaren för att interagera med och testa dina enheter och hantera plug and play-enheter.

## <a name="azure-iot-service-sdks"></a>Azure IoT-tjänst SDK:er

Det finns _tjänst-SDK:er_ tillgängliga för flera språk som gör att du kan skapa [backend-appar](#back-end-app) som interagerar med en IoT-hubb. IoT Hub-självstudierna visar hur du använder dessa service-SDK:er. Du hittar källkoden och ytterligare information om tjänsten SDK:er i den här [GitHub-databasen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT-verktyg

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett Visual Studio-kodtillägg med öppen källkod med öppen källkod som hjälper dig att hantera Azure IoT Hub och enheter i VS-kod. Med Azure IoT Tools kan IoT-utvecklare enkelt utveckla IoT-projekt i VS-kod.

## <a name="azure-portal"></a>Azure Portal

[Microsoft Azure-portalen](https://portal.azure.com) är en central plats där du kan etablera och hantera dina Azure-resurser. Det organiserar sitt innehåll med hjälp av _blad_.

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/overview) är en samling cmdlets som du kan använda för att hantera Azure med Windows PowerShell. Du kan använda cmdlets för att skapa, testa, distribuera och hantera lösningar och tjänster som levereras via Azure-plattformen.

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Med Azure Resource Manager](../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i din lösning som grupp. Du kan distribuera, uppdatera eller ta bort resurser för din lösning i en enda samordnad åtgärd.

## <a name="azure-service-bus"></a>Azure Service Bus

[Service Bus](../service-bus/index.md) tillhandahåller molnbaserad kommunikation med företagsmeddelanden och vidarebefordrad kommunikation som hjälper dig att ansluta lokala lösningar med molnet. Vissa IoT Hub-självstudier använder [servicebussköer](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage

[Azure Storage](../storage/common/storage-introduction.md) är en molnlagringslösning. Den innehåller tjänsten Blob Storage som du kan använda för att lagra ostrukturerade objektdata. Vissa IoT Hub-självstudier använder blob-lagring.

## <a name="back-end-app"></a>Backend-app

I samband med [IoT Hub](#iot-hub)är en backend-app en app som ansluter till en av de tjänstinriktade slutpunkterna på en IoT-hubb. En backend-app kan till exempel hämta meddelanden från [enhet till moln](#device-to-cloud) eller hantera [identitetsregistret](#identity-registry). Vanligtvis körs en backend-app i molnet, men i många av de självstudier är backend-apparna konsolappar som körs på din lokala utvecklingsdator.

## <a name="built-in-endpoints"></a>Inbyggda slutpunkter

Varje IoT-hubb innehåller en inbyggd [slutpunkt](iot-hub-devguide-endpoints.md) som är Event Hub-kompatibel. Du kan använda alla mekanismer som fungerar med händelsehubbar för att läsa meddelanden från den här slutpunkten.

## <a name="cloud-gateway"></a>Moln gateway

En molngateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). En molngateway finns i molnet i motsats till en [fältgateway](#field-gateway) som körs lokalt till dina enheter. Ett vanligt användningsfall för en molngateway är att implementera protokollöversättning för dina enheter.

## <a name="cloud-to-device"></a>Moln-till-enhet

Refererar till meddelanden som skickas från en IoT-hubb till en ansluten enhet. Ofta är dessa meddelanden kommandon som instruerar enheten att vidta en åtgärd. Mer information finns i [Skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguration

I kontexten för [automatisk enhetskonfiguration](iot-hub-auto-device-config.md)definierar en konfiguration inom IoT Hub önskad konfiguration för en uppsättning enhetstvillingar och tillhandahåller en uppsättning mått för att rapportera status och förlopp.

## <a name="connection-string"></a>Anslutningssträng

Du använder anslutningssträngar i appkoden för att kapsla in den information som krävs för att ansluta till en slutpunkt. En anslutningssträng innehåller vanligtvis slutpunktens adress och säkerhetsinformation, men anslutningssträngformaten varierar mellan tjänster. Det finns två typer av anslutningssträng som är associerade med IoT Hub-tjänsten:

- *Enhetsanslutningssträngar* gör det möjligt för enheter att ansluta till enhetsvända slutpunkter på en IoT-hubb.

- *IoT Hub-anslutningssträngar* gör det möjligt för backend-appar att ansluta till de tjänstinriktade slutpunkterna på en IoT-hubb.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan skapa anpassade [slutpunkter](iot-hub-devguide-endpoints.md) på en IoT-hubb för att leverera meddelanden som skickas av en [routningsregel](#routing-rules). Anpassade slutpunkter ansluter direkt till en händelsenav, en servicebusskö eller ett Service Bus-ämne.

## <a name="custom-gateway"></a>Anpassad gateway

En gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Du kan använda Azure IoT Edge för att skapa anpassade gateways som implementerar anpassad logik för att hantera meddelanden, anpassade protokollkonverteringar och annan bearbetning på kanten.

## <a name="data-point-message"></a>Meddelande om datapunkt

Ett datapunktsmeddelande är ett meddelande från [enhet till moln](#device-to-cloud) som innehåller [telemetridata](#telemetry) som vindhastighet eller temperatur.

## <a name="desired-configuration"></a>Önskad konfiguration

I samband med en [enhetstvilling](iot-hub-devguide-device-twins.md)refererar önskad konfiguration till den fullständiga uppsättningen egenskaper och metadata i enhetstvillingen som ska synkroniseras med enheten.

## <a name="desired-properties"></a>Önskade egenskaper

I kontexten för en [enhetstvilling](iot-hub-devguide-device-twins.md)är önskade egenskaper ett underavsnitt av enhetstvillingen som används med [rapporterade egenskaper](#reported-properties) för att synkronisera enhetens konfiguration eller tillstånd. Önskade egenskaper kan endast ställas in av en [backend-app](#back-end-app) och observeras av [enhetsappen](#device-app).

## <a name="device-to-cloud"></a>Enhet till moln

Refererar till meddelanden som skickas från en ansluten enhet till [IoT Hub](#iot-hub). Dessa meddelanden kan vara [datapunkt](#data-point-message) eller [interaktiva](#interactive-message) meddelanden. Mer information finns i [Skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Enhet

I IoT är en enhet vanligtvis en småskalig, fristående datorenhet som kan samla in data eller styra andra enheter. En enhet kan till exempel vara en miljöövervakningsenhet eller en styrenhet för bevattnings- och ventilationssystemen i ett växthus. [Enhetskatalogen](https://catalog.azureiotsolutions.com/) innehåller en lista över maskinvaruenheter som är certifierade för att fungera med [IoT Hub](#iot-hub).

## <a name="device-app"></a>Enhetsapp

En enhetsapp körs på [enheten](#device) och hanterar kommunikationen med din [IoT-hubb](#iot-hub). Vanligtvis använder du en av [Azure IoT-enhetSDK:er](#azure-iot-device-sdks) när du implementerar en enhetsapp. I många av IoT-självstudierna använder du en [simulerad enhet](#simulated-device) för enkelhetens skull.

## <a name="device-condition"></a>Enhetens tillstånd

Refererar till enhetstillståndsinformation, till exempel den anslutningsmetod som för närvarande används, som rapporterats av en [enhetsapp](#device-app). [Enhetsappar](#device-app) kan också rapportera sina funktioner. Du kan fråga efter tillstånds- och funktionsinformation med hjälp av enhetstvillingar.

## <a name="device-data"></a>Enhetsdata

Enhetsdata refererar till data per enhet som lagras i IoT [Hub-identitetsregistret](#identity-registry). Det är möjligt att importera och exportera dessa data.

## <a name="device-explorer"></a>Enhetsutforskaren

Enhetsutforskaren har ersatts med [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer), som används för att visa den telemetri som enheten skickar, arbeta med enhetsegenskaper och anropskommandon. Du kan också använda utforskaren för att interagera med och testa dina enheter och hantera plug and play-enheter.

## <a name="device-identity"></a>Enhetsidentitet

Enhetsidentiteten är den unika identifierare som tilldelats varje enhet som är registrerad i [identitetsregistret](#identity-registry).

## <a name="device-management"></a>Enhetshantering

Enhetshantering omfattar hela livscykeln som är associerad med hantering av enheterna i din IoT-lösning, inklusive planering, etablering, konfigurering, övervakning och pensionering.

## <a name="device-management-patterns"></a>Enhetshanteringsmönster

[IoT-hubben](#iot-hub) möjliggör vanliga enhetshanteringsmönster, inklusive omstart, fabriksåterställning och prestanda för uppdateringar av inbyggd programvara på dina enheter.

## <a name="device-rest-api"></a>REST-API för enhet

Du kan använda [ENHETENS REST API](https://docs.microsoft.com/rest/api/iothub/device) från en enhet för att skicka meddelanden från enhet till moln till en IoT-hubb och ta emot meddelanden från molnet till enheten från en IoT-hubb. [cloud-to-device](#cloud-to-device) Vanligtvis bör du använda en av de [SDK:er](#azure-iot-device-sdks) på högre nivå som visas i IoT Hub-självstudierna.

## <a name="device-provisioning"></a>Etablering av enhet

Enhetsetablering är processen att lägga till de första [enhetsdata](#device-data) till butikerna i din lösning. Om du vill aktivera en ny enhet för att ansluta till hubben måste du lägga till ett enhets-ID och nycklar i IoT [Hub-identitetsregistret](#identity-registry). Som en del av etableringsprocessen kan du behöva initiera enhetsspecifika data i andra lösningsarkiv.

## <a name="device-twin"></a>Enhetstvilling

En [enhetstvilling](iot-hub-devguide-device-twins.md) är JSON-dokument som lagrar enhetstillståndsinformation som metadata, konfigurationer och villkor. [IoT Hub](#iot-hub) beständiga en enhetstvilling för varje enhet som du etablerar i din IoT-hubb. Med enhetstvillingar kan du synkronisera [enhetsförhållanden](#device-condition) och konfigurationer mellan enheten och lösningens serverdel. Du kan fråga enhetstvillingar för att hitta specifika enheter och fråga status för tidskrävande åtgärder.

## <a name="direct-method"></a>Direkt metod

En [direkt metod](iot-hub-devguide-direct-methods.md) är ett sätt för dig att utlösa en metod för att köra på en enhet genom att anropa ett API på din IoT-hubb.

## <a name="endpoint"></a>Slutpunkt

En IoT-hubb visar flera [slutpunkter](iot-hub-devguide-endpoints.md) som gör att dina appar kan ansluta till IoT-hubben. Det finns enhetsinriktade slutpunkter som gör det möjligt för enheter att utföra åtgärder som att skicka [meddelanden från enhet till moln](#device-to-cloud) och ta emot meddelanden från molnet till [enheten.](#cloud-to-device) Det finns tjänstinriktade hanteringsslutpunkter som gör det möjligt för [backend-appar](#back-end-app) att utföra åtgärder som [enhetsidentitetshantering](#device-identity) och enhetsdvillinghantering. Det finns [serviceinriktade inbyggda slutpunkter](#built-in-endpoints) för att läsa meddelanden från enhet till moln. Du kan skapa [anpassade slutpunkter](#custom-endpoints) för att ta emot meddelanden från enhet till moln som skickas av en [routningsregel](#routing-rules).

## <a name="event-hubs-service"></a>Tjänsten Event Hubs

[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) är en mycket skalbar datainträngningstjänst som kan få in miljontals händelser per sekund. Med tjänsten kan du bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. En jämförelse med IoT Hub-tjänsten finns [i Jämförelse av Azure IoT Hub och Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Slutpunkt för händelsenavkompatibel

Om du vill läsa meddelanden från [enhet till moln som](#device-to-cloud) skickas till din IoT-hubb kan du ansluta till en slutpunkt på hubben och använda valfri Händelsehubbkompatibel metod för att läsa dessa meddelanden. Händelsehubbkompatibla metoder inkluderar användning av [SDK:erna för eventhubbar](../event-hubs/event-hubs-programming-guide.md) och [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Fältgateway

En fältgateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub) och vanligtvis distribueras lokalt med dina enheter. Mer information finns i [Vad är Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Kostnadsfritt konto

Du kan skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/) för att slutföra IoT Hub-självstudierna och experimentera med IoT Hub-tjänsten (och andra Azure-tjänster).

## <a name="gateway"></a>Gateway

En gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Se även [Fältgateway,](#field-gateway) [Molngateway](#cloud-gateway)och [Anpassad gateway](#custom-gateway).

## <a name="identity-registry"></a>Identitetsregister

[Identitetsregistret](iot-hub-devguide-identity-registry.md) är den inbyggda komponenten i en IoT-hubb som lagrar information om de enskilda enheter som tillåts ansluta till en IoT-hubb.

## <a name="interactive-message"></a>Interaktivt meddelande

Ett interaktivt meddelande är ett meddelande från [molnet till enheten](#cloud-to-device) som utlöser en omedelbar åtgärd i lösningens serverdel. En enhet kan till exempel skicka ett larm om ett fel som ska loggas in automatiskt i ett CRM-system.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub

IoT Hub är en fullständigt hanterad Azure-tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsback end. Mer information finns i [Vad är Azure IoT Hub?](about-iot-hub.md) Med din [Azure-prenumeration](#subscription)kan du skapa IoT-hubbar för att hantera dina IoT-meddelandearbetsbelastningar.

## <a name="iot-hub-metrics"></a>IoT Hub-mått

[IoT Hub-mått](iot-hub-metrics.md) ger dig data om tillståndet för IoT-hubbar i din [Azure-prenumeration](#subscription). IoT Hub-mått gör att du kan bedöma den övergripande hälsan för tjänsten och de enheter som är anslutna till den. IoT Hub-mått kan hjälpa dig att se vad som händer med din IoT-hubb och undersöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

## <a name="iot-hub-query-language"></a>Frågespråk för IoT Hub

[IoT Hub-frågespråket](iot-hub-devguide-query-language.md) är ett SQL-liknande språk [](#job) som gör att du kan fråga dina tvillingar och enhetstvillingar.

## <a name="iot-hub-resource-rest-api"></a>REST-API för IoT Hub-resurs

Du kan använda [REST-API:et för IoT Hub Resource](https://docs.microsoft.com/rest/api/iothub/iothubresource) REST för att hantera IoT-hubbar i din [Azure-prenumeration](#subscription) som att skapa, uppdatera och ta bort hubbar.

## <a name="iot-solution-accelerators"></a>IoT-lösningsacceleratorer

Azure IoT-lösningsacceleratorer paketerar flera Azure-tjänster till lösningar. Med de här lösningarna kan du komma igång snabbt med heltäckande implementeringar av vanliga IoT-scenarier. Mer information finns i [Vad är Azure IoT-lösningsacceleratorer?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>IoT-tillägget för Azure CLI 

[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är ett kommandoradsverktyg som inte är plattformen. Verktyget gör att du kan hantera dina enheter i [identitetsregistret,](#identity-registry)skicka och ta emot meddelanden och filer från dina enheter och övervaka din IoT-hubbåtgärder.

## <a name="job"></a>Jobb

Din lösningsbakdel kan använda [jobb](iot-hub-devguide-jobs.md) för att schemalägga och spåra aktiviteter på en uppsättning enheter som är registrerade med din IoT-hubb. Aktiviteter inkluderar uppdatering av önskade egenskaper för [enhetstvilling,](#desired-properties)uppdatering av enhetstvillingtaggar och anropar [direkta metoder](#direct-method). [tags](#tags) [IoT Hub](#iot-hub) använder också för [att importera till och exportera](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) från [identitetsregistret](#identity-registry).

## <a name="modules"></a>Moduler

På enhetssidan kan du med IoT Hub-enhetens SDK:er skapa [moduler](iot-hub-devguide-module-twins.md) där var och en öppnar en oberoende anslutning till IoT Hub. Med den här funktionen kan du använda separata namnområden för olika komponenter på enheten.

Modulidentitet och modultvilling ger samma funktioner som [enhetsidentitet](#device-identity) och [enhetstvilling](#device-twin) men med en finare granularitet. Den här finare granulariteten gör det möjligt för kompatibla enheter, till exempel operativsystembaserade enheter eller enheter för inbyggd programvara som hanterar flera komponenter, att isolera konfiguration och villkor för var och en av dessa komponenter.

## <a name="module-identity"></a>Modulidentitet

Modulidentiteten är den unika identifierare som tilldelats varje modul som tillhör en enhet. Modulidentitet registreras också i [identitetsregistret](#identity-registry).

## <a name="module-twin"></a>Modul tvilling

I likhet med enhetstvillingen är en modultvilling JSON-dokument som lagrar information om modultillstånd, till exempel metadata, konfigurationer och villkor. IoT Hub beständiga en modultvilling för varje modulidentitet som du etablerar under en enhetsidentitet i IoT-hubben. Med modultvillingar kan du synkronisera modulvillkor och konfigurationer mellan modulen och lösningens serverdel. Du kan fråga modultvillingar för att hitta specifika moduler och fråga status för tidskrävande åtgärder.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) är ett av de meddelandeprotokoll som [IoT Hub](#iot-hub) stöder för kommunikation med enheter. Mer information om meddelandeprotokollen som IoT Hub stöder finns i [Skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Övervakning av åtgärder

Med övervakning av IoT [Hub-åtgärder](iot-hub-operations-monitoring.md) kan du övervaka statusen för åtgärder på din IoT-hubb i realtid. [IoT Hub](#iot-hub) spårar händelser över flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier till en IoT Hub-slutpunkt för bearbetning. Du kan övervaka data för fel eller ställa in mer komplex bearbetning baserat på datamönster.

## <a name="physical-device"></a>Fysisk enhet

En fysisk enhet är en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb. För enkelhetens skull använder många av IoT Hub-självstudierna [simulerade enheter](#simulated-device) så att du kan köra exempel på din lokala dator.

## <a name="primary-and-secondary-keys"></a>Primära och sekundära nycklar

När du ansluter till en enhetsinriktad eller tjänstinriktad slutpunkt på en IoT-hubb innehåller [anslutningssträngen](#connection-string) nyckeln som ger dig åtkomst. När du lägger till en enhet i [identitetsregistret](#identity-registry) eller lägger till en [princip för delad åtkomst](#shared-access-policy) i navet genererar tjänsten en primär och sekundär nyckel. Med två nycklar kan du rulla över från en nyckel till en annan när du uppdaterar en nyckel utan att förlora åtkomsten till IoT-hubben.

## <a name="protocol-gateway"></a>Protokollgateway

En protokollgateway distribueras vanligtvis i molnet och tillhandahåller protokollöversättningstjänster för enheter som ansluter till [IoT Hub](#iot-hub). Mer information finns i [Vad är Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar

Det finns olika [kvoter](iot-hub-devguide-quotas-throttling.md) som gäller för din användning av [IoT Hub](#iot-hub), många av kvoterna varierar beroende på nivån för IoT-hubben. [IoT Hub](#iot-hub) tillämpar även [begränsningar](iot-hub-devguide-quotas-throttling.md) för din användning av tjänsten vid körning.

## <a name="reported-configuration"></a>Rapporterad konfiguration

I samband med en [enhetstvilling](iot-hub-devguide-device-twins.md)refererar rapporterad konfiguration till den fullständiga uppsättningen egenskaper och metadata i enhetstvillingen som ska rapporteras till lösningens serverdel.

## <a name="reported-properties"></a>Rapporterade egenskaper

I samband med en [enhetstvilling](iot-hub-devguide-device-twins.md)är rapporterade egenskaper ett underavsnitt av enhetstvillingen som används med [önskade egenskaper](#desired-properties) för att synkronisera enhetens konfiguration eller tillstånd. Rapporterade egenskaper kan endast ställas in av [enhetsappen](#device-app) och kan läsas och efterfrågas av en [backend-app](#back-end-app).

## <a name="resource-group"></a>Resursgrupp

[Azure Resource Manager](#azure-resource-manager) använder resursgrupper för att gruppera relaterade resurser tillsammans. Du kan använda en resursgrupp för att utföra åtgärder på alla resurser i gruppen samtidigt.

## <a name="retry-policy"></a>Återförsöksprincip

Du använder en princip för återförsök för att hantera [tillfälliga fel](/azure/architecture/best-practices/transient-faults) när du ansluter till en molntjänst.

## <a name="routing-rules"></a>Dirigeringsregler

Du konfigurerar [routningsregler](iot-hub-devguide-messages-read-custom.md) i IoT-hubben för att dirigera meddelanden från enhet till moln till en [inbyggd slutpunkt](#built-in-endpoints) eller till [anpassade slutpunkter](#custom-endpoints) för bearbetning av lösningens serverdel.

## <a name="sasl-plain"></a>SASL SLÄTT

SASL PLAIN är ett protokoll som AMQP-protokollet använder för att överföra säkerhetstoken.

## <a name="service-rest-api"></a>Tjänsten REST API

Du kan använda [SERVICE REST API](https://docs.microsoft.com/rest/api/iothub/service/configuration) från lösningens serverdel för att hantera dina enheter. Med API:et kan du hämta och uppdatera [enhetstvillingegenskaper,](#device-twin) anropa [direkta metoder](#direct-method)och schemalägga [jobb](#job). Vanligtvis bör du använda en av de [högre service-SDK:erna](#azure-iot-service-sdks) som visas i IoT Hub-självstudierna.

## <a name="shared-access-signature"></a>Signatur för delad åtkomst

SAS (Shared Access Signatures) är en autentiseringsmekanism som baseras på SHA-256-secure hashar eller URI:er. SAS-autentisering har två komponenter: en _princip för delad åtkomst_ och en signatur för delad _åtkomst_ (kallas ofta token). En enhet använder SAS för att autentisera med en IoT-hubb. [Backend-appar](#back-end-app) använder också SAS för att autentisera med de tjänstinriktade slutpunkterna på en IoT-hubb. Vanligtvis inkluderar du SAS-token i [anslutningssträngen](#connection-string) som en app använder för att upprätta en anslutning till en IoT-hubb.

## <a name="shared-access-policy"></a>Princip för delad åtkomst

En princip för delad åtkomst definierar de behörigheter som beviljas alla som har en giltig [primär eller sekundär nyckel](#primary-and-secondary-keys) som är associerad med den principen. Du kan hantera principer och nycklar för delad åtkomst för navet i [portalen](#azure-portal).

## <a name="simulated-device"></a>Simulerad enhet

För enkelhetens skull använder många av IoT Hub-självstudierna simulerade enheter så att du kan köra exempel på din lokala dator. En fysisk [enhet](#physical-device) är däremot en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb.

## <a name="solution"></a>Lösning
En _lösning_ kan referera till en Visual Studio-lösning som innehåller ett eller flera projekt. En _lösning_ kan också referera till en IoT-lösning som innehåller element som enheter, [enhetsappar,](#device-app)en IoT-hubb, andra [Azure-tjänster och backend-appar](#back-end-app).

## <a name="subscription"></a>Prenumeration

En Azure-prenumeration är där fakturering sker. Varje Azure-resurs som du skapar eller Azure-tjänst som du använder är associerad med en enda prenumeration. Många kvoter gäller också på prenumerationsnivå.

## <a name="system-properties"></a>Systemegenskaper

I samband med en [enhetstvilling](iot-hub-devguide-device-twins.md)är systemegenskaperna skrivskyddade och innehåller information om enhetsanvändningen, till exempel senaste aktivitetstid och anslutningstillstånd.

## <a name="tags"></a>Taggar

I samband med en [enhetstvilling](iot-hub-devguide-device-twins.md)är taggar enhetsmetadata som lagras och hämtas av lösningens baksida i form av ett JSON-dokument. Taggar är inte synliga för appar på en enhet.

## <a name="telemetry"></a>Telemetri

Enheter samlar in telemetridata, till exempel vindhastighet eller temperatur, och använder datapunktsmeddelanden för att skicka telemetrin till ett IoT-nav.

## <a name="token-service"></a>Token-tjänst

Du kan använda en tokentjänst för att implementera en autentiseringsmekanism för dina enheter. Den använder en IoT Hub [delad åtkomstprincip](#shared-access-policy) med **DeviceConnect-behörigheter** för att skapa *enhetsscoped* token. Dessa token gör det möjligt för en enhet att ansluta till din IoT-hubb. En enhet använder en anpassad autentiseringsmekanism för att autentisera med tokentjänsten. Om enheten autentiserar har den tokentjänsten en SAS-token som enheten kan använda för att komma åt din IoT-hubb.

## <a name="twin-queries"></a>Dubbla frågor

[Enhets- och modultvillingfrågor](iot-hub-devguide-query-language.md) använder det SQL-liknande IoT Hub-frågespråket för att hämta information från enhetstvillingar eller modultvillingar. Du kan använda samma IoT Hub-frågespråk för att hämta information om [](#job) hur du kör i IoT-hubben.

## <a name="twin-synchronization"></a>Dubbel synkronisering

Dubbel synkronisering använder [de önskade egenskaperna](#desired-properties) i enhetens tvillingar eller modultvillingar för att konfigurera dina enheter eller moduler och hämta rapporterade [egenskaper](#reported-properties) från dem för att lagra i tvillingen.

## <a name="x509-client-certificate"></a>X.509-klientcertifikat

En enhet kan använda ett X.509-certifikat för att autentisera med [IoT Hub](#iot-hub). Att använda ett X.509-certifikat är ett alternativ till att använda en [SAS-token](#shared-access-signature).
