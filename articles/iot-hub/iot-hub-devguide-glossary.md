---
title: Terminologi för Azure IoT Hub ord lista | Microsoft Docs
description: Guide för utvecklare – en ord lista som förklarar några av de vanliga termer som används i Azure IoT Hub-artiklarna.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 73981cb0f124e1577690f893ead60e6375f16298
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133133"
---
# <a name="glossary-of-iot-hub-terms"></a>Ord lista för IoT Hub villkor

Den här artikeln innehåller några av de vanliga termer som används i IoT Hub artiklar.

## <a name="advanced-message-queueing-protocol"></a>Avancerat protokoll för meddelandekö

[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) är ett av de meddelande protokoll som [IoT Hub](#iot-hub) stöder för att kommunicera med enheter. Mer information om de meddelande protokoll som IoT Hub stöder finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatisk enhets hantering

Automatisk enhets hantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna i hanteringen av stora enhets flottor över hela livs cykeln. Med automatisk enhets hantering kan du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de kommer till omfånget.  Består av [Automatisk enhets konfiguration](iot-hub-auto-device-config.md) och [IoT Edge automatiska distributioner](../iot-edge/how-to-deploy-at-scale.md).

## <a name="automatic-device-configuration"></a>Automatisk enhets konfiguration

Server delen av lösningen kan använda [automatiska enhets konfigurationer](iot-hub-auto-device-config.md) för att tilldela önskade egenskaper till en uppsättning [enheter, dubbla](#device-twin) och rapportera status med hjälp av system mått och anpassade mått. 

## <a name="azure-classic-cli"></a>Klassisk Azure CLI

Det [klassiska CLI](../cli-install-nodejs.md) -gränssnittet i Azure är ett plattforms oberoende kommando verktyg med öppen källkod för att skapa och hantera resurser i Microsoft Azure. Den här versionen av CLI bör endast användas för klassiska distributioner.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) är ett kommando verktyg med öppen källkod med öppen källkod för att skapa och hantera resurser i Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>SDK: er för Azure IoT-enheter

Det finns tillgängliga _enhets-SDK_ : er för flera språk som gör att du kan skapa [enhets program](#device-app) som interagerar med en IoT-hubb. IoT Hub självstudier visar hur du använder dessa enhets-SDK: er. Du hittar käll koden och ytterligare information om enhets-SDK: erna i den här GitHub- [lagringsplatsen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-explorer"></a>Azure IoT Explorer

[Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) används för att Visa telemetri som enheten skickar, arbeta med enhets egenskaper och anropa kommandon. Du kan också använda Utforskaren för att interagera med och testa dina enheter och för att hantera plug and Play-enheter.

## <a name="azure-iot-service-sdks"></a>SDK: er för Azure IoT-tjänster

Det finns _tjänst-SDK_ : er för flera språk som gör att du kan skapa [backend-appar](#back-end-app) som interagerar med en IoT-hubb. IoT Hub självstudier visar hur du använder dessa tjänst-SDK: er. Du hittar käll koden och ytterligare information om tjänst-SDK: er i den här GitHub- [lagringsplatsen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT-verktyg

[Azure IoT-verktygen](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett plattforms oberoende Visual Studio Code-tillägg med öppen källkod som hjälper dig att hantera Azure-IoT Hub och enheter i vs Code. Med Azure IoT-verktyg kan IoT-utvecklare utveckla IoT-projekt i VS Code enkelt.

## <a name="azure-portal"></a>Azure Portal

[Microsoft Azure-portalen](https://portal.azure.com) är en central plats där du kan etablera och hantera dina Azure-resurser. Den ordnar sitt innehåll med hjälp av _blad_.

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/overview) är en samling cmdlets som du kan använda för att hantera Azure med Windows PowerShell. Du kan använda cmdletarna för att skapa, testa, distribuera och hantera lösningar och tjänster som levereras via Azure-plattformen.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Med [Azure Resource Manager](../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i din lösning som en grupp. Du kan distribuera, uppdatera eller ta bort resurser för lösningen i en enda, koordinerad åtgärd.

## <a name="azure-service-bus"></a>Azure Service Bus

[Service Bus](../service-bus/index.md) tillhandahåller moln aktive rad kommunikation med företags meddelande tjänster och vidarebefordrad kommunikation som hjälper dig att ansluta lokala lösningar med molnet. Vissa IoT Hub själv studie kurser använder Service Bus [köer](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage

[Azure Storage](../storage/common/storage-introduction.md) är en lösning för moln lagring. Den innehåller Blob Storage tjänst som du kan använda för att lagra ostrukturerade objekt data. Vissa IoT Hub själv studie kurser använder Blob Storage.

## <a name="back-end-app"></a>Backend-app

I samband med [IoT Hub](#iot-hub)är en backend-app en app som ansluter till en av de service riktade slut punkter som finns på en IoT-hubb. Till exempel kan en backend-app hämta meddelanden [från enheten till molnet](#device-to-cloud) eller hantera [identitets registret](#identity-registry). Normalt körs en backend-app i molnet, men i många av de självstudierna är de konsol program som körs på den lokala utvecklings datorn.

## <a name="built-in-endpoints"></a>Inbyggda slut punkter

Varje IoT-hubb innehåller en inbyggd [slut punkt](iot-hub-devguide-endpoints.md) som är kompatibel med Event Hub. Du kan använda valfri mekanism som fungerar med Event Hubs för att läsa meddelanden från enheten till molnet från den här slut punkten.

## <a name="cloud-gateway"></a>Cloud Gateway

En molnbaserad Gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). En moln-Gateway finns i molnet i motsats till en [fält-Gateway](#field-gateway) som körs lokalt på dina enheter. Ett vanligt användnings fall för en moln-Gateway är att implementera protokoll översättning för dina enheter.

## <a name="cloud-to-device"></a>Moln till enhet

Avser meddelanden som skickas från en IoT-hubb till en ansluten enhet. Dessa meddelanden är ofta kommandon som instruerar enheten att vidta en åtgärd. Mer information finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguration

I samband med [Automatisk enhets konfiguration](iot-hub-auto-device-config.md)definierar en konfiguration i IoT Hub den önskade konfigurationen för en uppsättning enheter är tillsammans med en uppsättning mått för att rapportera status och förlopp.

## <a name="connection-string"></a>Anslutningssträng

Du använder anslutnings strängar i din app-kod för att kapsla in den information som krävs för att ansluta till en slut punkt. En anslutnings sträng innehåller vanligt vis adressen till slut punkten och säkerhets informationen, men anslutnings sträng formaten varierar mellan olika tjänster. Det finns två typer av anslutnings strängar kopplade till den IoT Hub tjänsten:

- *Anslutnings strängar* för enheter gör det möjligt för enheter att ansluta till enhets riktade slut punkter på en IoT-hubb.

- *IoT Hub anslutnings strängar* aktivera backend-appar för att ansluta till tjänstens slut punkter på en IoT-hubb.

## <a name="custom-endpoints"></a>Anpassade slut punkter

Du kan skapa anpassade [slut punkter](iot-hub-devguide-endpoints.md) i en IoT-hubb för att leverera meddelanden som skickas av en [regel för routning](#routing-rules). Anpassade slut punkter ansluter direkt till en Event Hub, en Service Bus kö eller ett Service Bus ämne.

## <a name="custom-gateway"></a>Anpassad Gateway

En Gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Du kan använda Azure IoT Edge för att bygga anpassade gatewayer som implementerar anpassad logik för att hantera meddelanden, anpassade protokoll konverteringar och annan bearbetning i kanten.

## <a name="data-point-message"></a>Meddelande om data punkt

Ett data punkts meddelande är ett [enhets-till-moln-](#device-to-cloud) meddelande som innehåller [telemetridata](#telemetry) som vridnings hastighet eller temperatur.

## <a name="desired-configuration"></a>Önskad konfiguration

I kontexten för en [enhet](iot-hub-devguide-device-twins.md), refererar önskad konfiguration till en fullständig uppsättning egenskaper och metadata i enheten, som ska synkroniseras med enheten.

## <a name="desired-properties"></a>Önskade egenskaper

I kontexten för en [enhet](iot-hub-devguide-device-twins.md), är önskade egenskaper ett underavsnitt av den enhet som används med [rapporterade egenskaper](#reported-properties) för att synkronisera enhetens konfiguration eller villkor. Önskade egenskaper kan bara ställas in av en [backend-app](#back-end-app) och observeras av [enhets appen](#device-app).

## <a name="device-to-cloud"></a>Enhet till moln

Avser meddelanden som skickas från en ansluten enhet till [IoT Hub](#iot-hub). Dessa meddelanden kan vara [data punkts-](#data-point-message) eller [interaktiva](#interactive-message) meddelanden. Mer information finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Enhet

I IoT-kontexten är en enhet vanligt vis en småskalig, fristående data behandlings enhet som kan samla in data eller styra andra enheter. En enhet kan till exempel vara en miljö övervaknings enhet eller en kontrollant för vatten-och ventilations system i en växthus. [Enhets katalogen](https://catalog.azureiotsolutions.com/) innehåller en lista över maskin varu enheter som är certifierade för att fungera med [IoT Hub](#iot-hub).

## <a name="device-app"></a>Enhetsapp

En enhets App körs på [enheten](#device) och hanterar kommunikationen med [IoT Hub](#iot-hub). Normalt använder du en av [Azure IoT-enhetens SDK](#azure-iot-device-sdks) : er när du implementerar en enhets app. I många av IoT-självstudierna använder du en [simulerad enhet](#simulated-device) för bekvämlighet.

## <a name="device-condition"></a>Enhets villkor

Avser enhets tillståndsinformation, till exempel anslutnings metoden som används för närvarande, som rapporteras av en [app i enheten](#device-app). [Enhets appar](#device-app) kan också rapportera sina funktioner. Du kan fråga efter villkor och kapacitets information med hjälp av enhets dubbla.

## <a name="device-data"></a>Enhets data

Enhets data refererar till de enhets data som lagras i IoT Hub [identitets registret](#identity-registry). Det går att importera och exportera dessa data.

## <a name="device-explorer"></a>Enhetsutforskaren

Device Explorer har ersatts med [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer), som används för att Visa telemetri som enheten skickar, arbeta med enhets egenskaper och anropa kommandon. Du kan också använda Utforskaren för att interagera med och testa dina enheter och för att hantera plug and Play-enheter.

## <a name="device-identity"></a>Enhetsidentitet

Enhets identiteten är den unika identifierare som tilldelats varje enhet som är registrerad i [identitets registret](#identity-registry).

## <a name="device-management"></a>Enhetshantering

Enhets hantering omfattar hela livs cykeln som är kopplad till hanteringen av enheterna i din IoT-lösning, inklusive planering, etablering, konfiguration, övervakning och pensionering.

## <a name="device-management-patterns"></a>Enhetshanteringsmönster

[IoT Hub](#iot-hub) möjliggör vanliga enhets hanterings mönster, inklusive omstart, återställning av fabriks uppdateringar och uppdatering av inbyggd program vara på dina enheter.

## <a name="device-rest-api"></a>Enhets REST API

Du kan använda [enhets REST API](https://docs.microsoft.com/rest/api/iothub/device) från en enhet för att skicka meddelanden från enheten till molnet till en IoT-hubb och ta emot meddelanden från [molnet till enheten](#cloud-to-device) från en IoT-hubb. Normalt bör du använda en av de högre [enhets-SDK: erna](#azure-iot-device-sdks) som visas i självstudierna för IoT Hub.

## <a name="device-provisioning"></a>Enhets etablering

Enhets etablering är en process för att lägga till den ursprungliga [enhets informationen](#device-data) i butikerna i din lösning. Om du vill aktivera en ny enhet för att ansluta till navet måste du lägga till ett enhets-ID och nycklar i IoT Hub [identitets registret](#identity-registry). Som en del av etablerings processen kan du behöva initiera enhetsspecifika data i andra lösnings lager.

## <a name="device-twin"></a>Enhetstvilling

En [enhet med dubbla](iot-hub-devguide-device-twins.md) är JSON-dokument som lagrar information om enhets tillstånd, till exempel metadata, konfigurationer och villkor. [IoT Hub](#iot-hub) sparar en enhet för varje enhet som du etablerar i IoT Hub. Med enhets dubbla kan du synkronisera [enhets villkor](#device-condition) och konfigurationer mellan enheten och Server delen för lösningen. Du kan fråga enheten för att hitta vissa enheter och fråga efter statusen för långvariga åtgärder.

## <a name="direct-method"></a>Direkt metod

En [direkt metod](iot-hub-devguide-direct-methods.md) är ett sätt för dig att utlösa en metod som ska köras på en enhet genom att anropa ett API i IoT Hub.

## <a name="endpoint"></a>Slutpunkt

En IoT-hubb visar flera [slut punkter](iot-hub-devguide-endpoints.md) som gör det möjligt för dina appar att ansluta till IoT Hub. Det finns enhets slut punkter som gör det möjligt för enheter att utföra åtgärder som att skicka meddelanden [från enheten till molnet](#device-to-cloud) och ta emot meddelanden från [molnet till enheten](#cloud-to-device) . Det finns slut punkter för hantering av tjänster som gör att [backend-appar](#back-end-app) kan utföra åtgärder som hantering av [enhetens identitet](#device-identity) och enhetens dubbla hantering. Det finns tjänstbaserade [inbyggda slut punkter](#built-in-endpoints) för att läsa meddelanden från enheten till molnet. Du kan skapa [anpassade slut punkter](#custom-endpoints) för att ta emot meddelanden från enheten till molnet som skickas av en [regel för routning](#routing-rules).

## <a name="event-hubs-service"></a>Event Hubs tjänst

[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) är en mycket skalbar data ingress-tjänst som kan mata in miljon tals händelser per sekund. Med tjänsten kan du bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. En jämförelse med IoT Hub-tjänsten finns i [jämförelse av azure IoT Hub och Azure-Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Event Hub – kompatibel slut punkt

Om du vill läsa meddelanden från [enheten till molnet](#device-to-cloud) som skickas till din IoT-hubb kan du ansluta till en slut punkt på hubben och använda valfri Event Hub-kompatibel metod för att läsa dessa meddelanden. Event Hub-kompatibla metoder omfattar användning av [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) : er och [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Fält-Gateway

Med en fält-Gateway kan du ansluta till enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub) och distribueras vanligt vis lokalt med dina enheter. Mer information finns i [Vad är Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Kostnadsfritt konto

Du kan skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/) för att slutföra IoT Hub självstudier och experimentera med tjänsten IoT Hub (och andra Azure-tjänster).

## <a name="gateway"></a>Gateway

En Gateway möjliggör anslutning för enheter som inte kan ansluta direkt till [IoT Hub](#iot-hub). Se även [Field Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway)och [anpassad Gateway](#custom-gateway).

## <a name="identity-registry"></a>Identitets register

[Identitets registret](iot-hub-devguide-identity-registry.md) är den inbyggda komponenten i en IoT-hubb som lagrar information om de enskilda enheter som tillåts att ansluta till en IoT-hubb.

## <a name="interactive-message"></a>Interaktivt meddelande

Ett interaktivt meddelande är ett [moln-till-enhet-](#cloud-to-device) meddelande som utlöser en omedelbar åtgärd i Server delen av lösningen. En enhet kan till exempel skicka ett larm om ett haveri som ska loggas in automatiskt på ett CRM-system.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub

IoT Hub är en helt hanterad Azure-tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och en lösnings Server del. Mer information finns i [Vad är Azure IoT Hub?](about-iot-hub.md) Med din [Azure-prenumeration](#subscription)kan du skapa IoT-hubbar för att hantera dina IoT Messaging-arbetsbelastningar.

## <a name="iot-hub-metrics"></a>IoT Hub mått

[IoT Hub mått](iot-hub-metrics.md) ger dig information om status för IoT-hubbarna i din [Azure-prenumeration](#subscription). Med IoT Hub mått kan du utvärdera den övergripande hälsan för tjänsten och de enheter som är anslutna till den. IoT Hub mått kan hjälpa dig att se vad som händer med din IoT-hubb och undersöka rotor Saks problem utan att behöva kontakta Azure-supporten.

## <a name="iot-hub-query-language"></a>IoT Hub frågespråk

[IoT Hub frågespråk](iot-hub-devguide-query-language.md) är ett SQL-liknande språk som gör att du kan söka efter [](#job) och från enheten.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub resurs REST API

Du kan använda [IoT Hub resurs REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) för att hantera IoT-hubbar i din [Azure-prenumeration](#subscription) som utför åtgärder som att skapa, uppdatera och ta bort hubbar.

## <a name="iot-solution-accelerators"></a>IoT-lösningsacceleratorer

Azure IoT Solution Accelerators-paket tillsammans med flera Azure-tjänster i lösningar. Med dessa lösningar kan du snabbt komma igång med implementeringar från slut punkt till slut punkt av vanliga IoT-scenarier. Mer information finns i [Vad är acceleratorer för Azure IoT-lösningar?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>IoT-tillägget för Azure CLI 

[IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) är ett kommando rads verktyg för flera plattformar. Med verktyget kan du hantera dina enheter i [identitets registret](#identity-registry), skicka och ta emot meddelanden och filer från dina enheter och övervaka dina IoT Hub-åtgärder.

## <a name="job"></a>Jobb

Server delen av lösningen kan använda [jobb](iot-hub-devguide-jobs.md) för att schemalägga och spåra aktiviteter på en uppsättning enheter som är registrerade i IoT Hub. Aktiviteterna omfattar att uppdatera enhetens dubbla [önskade egenskaper](#desired-properties), uppdatera enhetens dubbla [taggar](#tags)och anropa [direkta metoder](#direct-method). [IoT Hub](#iot-hub) använder också för att [Importera till och exportera](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) från [identitets registret](#identity-registry).

## <a name="modules"></a>Moduler

På enhets sidan kan du med IoT Hub enhets-SDK: er skapa [moduler](iot-hub-devguide-module-twins.md) där var och en öppnar en oberoende anslutning till IoT Hub. Med den här funktionen kan du använda separata namn rymder för olika komponenter på enheten.

Modul identitet och modul dubbla ger samma funktioner som [enhets identitet](#device-identity) och [enhet](#device-twin) , men med en bättre precision. Med den här bättre precisionen kan enheter, till exempel operativ systembaserade enheter eller inbyggda enheter hantera flera komponenter, för att isolera konfiguration och villkor för var och en av dessa komponenter.

## <a name="module-identity"></a>Modulens identitet

Modulens identitet är den unika identifierare som tilldelats varje modul som tillhör en enhet. Modulens identitet registreras också i [identitets registret](#identity-registry).

## <a name="module-twin"></a>Modul, delad

På samma sätt som enhets dubbla är en modul i ett JSON-dokument som lagrar information om modulens tillstånd, till exempel metadata, konfigurationer och villkor. IoT Hub behåller en modul som är sammanflätad för varje modul identitet som du etablerar under en enhets identitet i din IoT-hubb. I modulen är det möjligt att synkronisera modulens villkor och konfigurationer mellan modulen och Server delen för lösningen. Du kan köra en fråga om modulerna för att hitta vissa moduler och fråga efter statusen för långvariga åtgärder.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) är ett av de meddelande protokoll som [IoT Hub](#iot-hub) stöder för att kommunicera med enheter. Mer information om de meddelande protokoll som IoT Hub stöder finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Övervakning av åtgärder

Med IoT Hub [Åtgärds övervakning](iot-hub-operations-monitoring.md) kan du övervaka statusen för åtgärder i IoT Hub i real tid. [IoT Hub](#iot-hub) spårar händelser i flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier till en IoT Hub-slutpunkt för bearbetning. Du kan övervaka data för fel eller ställa in mer komplex bearbetning baserat på data mönster.

## <a name="physical-device"></a>Fysisk enhet

En fysisk enhet är en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb. För enkelhetens skull använder många av de IoT Hub självstudierna [simulerade enheter](#simulated-device) för att kunna köra exempel på din lokala dator.

## <a name="primary-and-secondary-keys"></a>Primära och sekundära nycklar

När du ansluter till en enhets riktad eller riktad slut punkt på en IoT-hubb innehåller [anslutnings strängen](#connection-string) nyckeln för att ge dig åtkomst. När du lägger till en enhet i [identitets registret](#identity-registry) eller lägger till en [princip för delad åtkomst](#shared-access-policy) i din hubb, genererar tjänsten en primär och sekundär nyckel. Med två nycklar kan du återställa från en nyckel till en annan när du uppdaterar en nyckel utan att förlora åtkomsten till IoT Hub.

## <a name="protocol-gateway"></a>Protokollgateway

En protokoll-Gateway distribueras vanligt vis i molnet och tillhandahåller protokoll översättnings tjänster för enheter som ansluter till [IoT Hub](#iot-hub). Mer information finns i [Vad är Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar

Det finns olika [kvoter](iot-hub-devguide-quotas-throttling.md) som gäller för din användning av [IoT Hub](#iot-hub), många av kvoterna varierar beroende på nivån i IoT Hub. [IoT Hub](#iot-hub) använder också [begränsningar](iot-hub-devguide-quotas-throttling.md) för din användning av tjänsten vid körning.

## <a name="reported-configuration"></a>Rapporterad konfiguration

I samband med en [enhet](iot-hub-devguide-device-twins.md), refererar konfigurationen till en fullständig uppsättning egenskaper och metadata i den enhet som ska rapporteras till lösningens Server del.

## <a name="reported-properties"></a>Rapporterade egenskaper

I kontexten för en [enhet](iot-hub-devguide-device-twins.md), är rapporterade egenskaper ett underavsnitt av enheten, som används med [önskade egenskaper](#desired-properties) för att synkronisera enhetens konfiguration eller villkor. Rapporterade egenskaper kan bara ställas in av [enhets appen](#device-app) och kan läsas och frågas av en [backend-app](#back-end-app).

## <a name="resource-group"></a>Resursgrupp

[Azure Resource Manager](#azure-resource-manager) använder resurs grupper för att gruppera relaterade resurser tillsammans. Du kan använda en resurs grupp för att utföra åtgärder på alla resurser i gruppen samtidigt.

## <a name="retry-policy"></a>Återförsöksprincip

Du använder en princip för återförsök för att hantera [tillfälliga fel](/azure/architecture/best-practices/transient-faults) när du ansluter till en moln tjänst.

## <a name="routing-rules"></a>Dirigeringsregler

Du konfigurerar [regler för routning](iot-hub-devguide-messages-read-custom.md) i IoT-hubben så att de dirigerar enhets-till-moln-meddelanden till en [inbyggd slut punkt](#built-in-endpoints) eller [anpassade slut punkter](#custom-endpoints) för bearbetning av Server delen av lösningen.

## <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN är ett protokoll som används av AMQP-protokollet för att överföra säkerhetstoken.

## <a name="service-rest-api"></a>Tjänsten REST API

Du kan använda [tjänst REST API](https://docs.microsoft.com/rest/api/iothub/service/configuration) från lösningens Server del för att hantera dina enheter. Med API: et kan du hämta och uppdatera [enhetens dubbla](#device-twin) egenskaper, anropa [direkta metoder](#direct-method)och schemalägga [jobb](#job). Normalt bör du använda en av de högre [service SDK: erna](#azure-iot-service-sdks) som visas i självstudierna för IoT Hub.

## <a name="shared-access-signature"></a>Signatur för delad åtkomst

Signaturer för delad åtkomst (SAS) är en autentiseringsmekanism baserad på SHA-256-säkra hash-värden eller URI: er. SAS-autentisering har två komponenter: en _delad åtkomst princip_ och en _signatur för delad åtkomst_ (kallas ofta för en token). En enhet använder SAS för att autentisera med en IoT-hubb. [Backend-appar](#back-end-app) använder också SAS för att autentisera med tjänstens slut punkter på en IoT-hubb. Normalt inkluderar du SAS-token i [anslutnings strängen](#connection-string) som en app använder för att upprätta en anslutning till en IoT-hubb.

## <a name="shared-access-policy"></a>Princip för delad åtkomst

En princip för delad åtkomst definierar de behörigheter som beviljats för alla som har en giltig [primär eller sekundär nyckel](#primary-and-secondary-keys) som är associerad med principen. Du kan hantera principer och nycklar för delad åtkomst för hubben i [portalen](#azure-portal).

## <a name="simulated-device"></a>Simulerad enhet

För enkelhetens skull använder många av de IoT Hub självstudierna simulerade enheter för att kunna köra exempel på din lokala dator. En [fysisk enhet](#physical-device) är däremot en riktig enhet, till exempel en Raspberry Pi som ansluter till en IoT-hubb.

## <a name="solution"></a>Lösning
En _lösning_ kan referera till en Visual Studio-lösning som innehåller ett eller flera projekt. En _lösning_ kan också referera till en IoT-lösning som innehåller element som enheter, [enhets appar](#device-app), en IoT-hubb, andra Azure-tjänster och [backend-appar](#back-end-app).

## <a name="subscription"></a>Prenumeration

En Azure-prenumeration är den plats där faktureringen sker. Varje Azure-resurs som du skapar eller Azure-tjänsten som du använder är associerad med en enda prenumeration. Många kvoter gäller även för en prenumerations nivå.

## <a name="system-properties"></a>Systemegenskaper

I en [enhets](iot-hub-devguide-device-twins.md)kontext är system egenskaperna skrivskyddade och innehåller information om enhets användningen, till exempel senaste aktivitets tid och anslutnings tillstånd.

## <a name="tags"></a>Taggar

I en [enhets](iot-hub-devguide-device-twins.md)kontext är Taggar enhetens metadata som lagras och hämtas av lösningens Server del i form av ett JSON-dokument. Taggarna är inte synliga för appar på en enhet.

## <a name="telemetry"></a>Telemetri

Enheter samlar in telemetridata, till exempel vridnings hastighet eller temperatur, och använder data punkt meddelanden för att skicka telemetri till en IoT-hubb.

## <a name="token-service"></a>Token service

Du kan använda en token-tjänst för att implementera en autentiseringsmekanism för dina enheter. Den använder en IoT Hub [princip för delad åtkomst](#shared-access-policy) med **DeviceConnect** -behörigheter för att skapa *enhets omfång* . Dessa token gör det möjligt för en enhet att ansluta till IoT Hub. En enhet använder en anpassad autentiseringsmekanism för att autentisera med token-tjänsten. Om enheten autentiseras, utfärdar token-tjänsten en SAS-token för enheten som ska användas för att få åtkomst till din IoT-hubb.

## <a name="twin-queries"></a>Dubbla frågor

[Enhets-och modulernas dubbla frågor](iot-hub-devguide-query-language.md) använder SQL-like IoT Hub frågespråk för att hämta information från enheten med dubbla eller flera moduler. Du kan använda samma språk för IoT Hub frågor för att hämta information [](#job) om hur du kör i IoT Hub.

## <a name="twin-synchronization"></a>Dubbel synkronisering

Den dubbla synkroniseringen använder de [önskade egenskaperna](#desired-properties) i enheten, med dubbla eller moduler, så att du kan konfigurera enheter eller moduler och hämta [rapporterade egenskaper](#reported-properties) från dem till Store i den dubbla.

## <a name="x509-client-certificate"></a>X. 509 klient certifikat

En enhet kan använda ett X. 509-certifikat för att autentisera med [IoT Hub](#iot-hub). Att använda ett X. 509-certifikat är ett alternativ till att använda en [SAS-token](#shared-access-signature).
