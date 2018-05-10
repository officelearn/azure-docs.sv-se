---
title: Azure IoT-hubb ordlista | Microsoft Docs
description: Utvecklarhandbok - en ordlista över vanliga villkoren för Azure IoT Hub.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: aae72618da42db53304075506b4969945ff0165f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="glossary-of-iot-hub-terms"></a>Ordlista IoT-hubb
Den här artikeln innehåller några av de vanliga termer som används i IoT-hubb-artiklar.

## <a name="advanced-message-queueing-protocol"></a>Avancerade Message Queueing-protokoll
[Avancerade Message Queueing Protocol (AMQP)](https://www.amqp.org/) är en av de messaging protokoll som [IoT-hubb](#iot-hub) har stöd för att kommunicera med enheter. Läs mer om meddelanden protokoll som stöds i IoT-hubb [skicka och ta emot meddelanden med IoT-hubben](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatisk enhetshantering
Automatisk hantering av enheter i Azure IoT Hub automatiserar många av återkommande och komplicerade uppgifter för att hantera stora enheten flottor via deras livscykler i sin helhet. Med automatisk hantering av enheter du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT-hubb uppdatera enheter när de kommer inom omfånget.  Består av [automatisk enhetskonfigurationer](iot-hub-auto-device-config.md) och [IoT kant automatiska uppdateringar](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Automatisk enhetskonfigurationen
Din lösningens serverdel kan använda [automatisk enhetskonfigurationer](iot-hub-auto-device-config.md) att tilldela en uppsättning egenskaper [enhet twins](#device-twin) och rapportera status med hjälp av system mått och anpassade mått. 

## <a name="azure-cli"></a>Azure CLI
Den [Azure CLI](../cli-install-nodejs.md) är ett kommandoradsverktyg för plattformsoberoende, öppen källkod, shell-baserade, för att skapa och hantera resurser i Microsoft Azure. Den här versionen av CLI implementeras med hjälp av Node.js.

## <a name="azure-cli-20"></a>Azure CLI 2.0
Den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) är ett kommandoradsverktyg för plattformsoberoende, öppen källkod, shell-baserade, för att skapa och hantera resurser i Microsoft Azure. Den här förhandsversionen av CLI implementeras med hjälp av Python.


## <a name="azure-iot-device-sdks"></a>Azure IoT-enhet SDK
Det finns _enheten SDK_ tillgänglig för flera språk som du kan skapa [enhetsappar](#device-app) som interagerar med en IoT-hubb. IoT-hubb självstudiekurser visar hur du använder dessa enheter SDK: er. Du hittar källkoden och ytterligare information om enheten SDK: er i den här GitHub [databasen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDK
Det finns _SDK-tjänsten_ tillgänglig för flera språk som du kan skapa [backend-appar](#back-end-app) som interagerar med en IoT-hubb. IoT-hubb självstudiekurser visar hur du använder dessa service SDK: er. Du hittar källkoden och ytterligare information om SDK: er för tjänsten i den här GitHub [databasen](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-portal"></a>Azure Portal
Den [Microsoft Azure-portalen](https://portal.azure.com) är en central plats där du kan etablera och hantera Azure-resurser. Den organiserar dess innehåll med hjälp av _blad_.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) är en samling av du kan använda för att hantera Azure med Windows PowerShell-cmdlets. Du kan använda cmdletar för att skapa, testa, distribuera och hantera lösningar och tjänster som levereras via Azure-plattformen.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i din lösning som en grupp. Du kan distribuera, uppdatera eller ta bort resurserna för lösningen i en enda, samordnad åtgärd.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) här moln-aktiverat kommunikation med meddelandetjänster för företag och vidarebefordrande kommunikation som hjälper dig att ansluta lokala lösningar med molnet. Vissa IoT-hubb självstudier att använda Service Bus [köer](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md) är en lagringslösning för molnet. Den innehåller Blob Storage-tjänst som du kan använda för att lagra Ostrukturerade objektdata. Vissa IoT-hubb självstudier använda blob storage.

## <a name="back-end-app"></a>Backend-app
I samband med [IoT-hubb](#iot-hub), en backend-app är en app som ansluter till en av slutpunkterna service-inriktad på en IoT-hubb. Till exempel en backend-app kan hämta [enhet till moln](#device-to-cloud)meddelanden eller hantera de [identitetsregistret](#identity-registry). Normalt en backend-app som körs i molnet, men i många av självstudierna backend-appar konsolappar som körs på utvecklingsdatorn lokala.

## <a name="built-in-endpoints"></a>Inbyggda slutpunkter
Alla IoT-hubb innehåller en inbyggd [endpoint](iot-hub-devguide-endpoints.md) som Event Hub-kompatibel. Du kan använda någon mekanism som fungerar med Händelsehubbar att läsa meddelanden från enhet till moln från den här slutpunkten.

## <a name="cloud-gateway"></a>Gateway för moln
En molngateway upprättar anslutningarna för enheter som inte kan ansluta direkt till [IoT-hubb](#iot-hub). En molngateway finns i molnet jämfört med en [fältet gateway](#field-gateway) som körs lokalt till dina enheter. Ett vanligt användningsfall för en molngateway är att implementera översättning av protokollet för dina enheter.

## <a name="cloud-to-device"></a>Moln till enhet
Refererar till meddelanden som skickas från en IoT-hubb till en ansluten enhet. Dessa meddelanden är ofta kommandon som instruerar enheten att vidta en åtgärd. Mer information finns i [skicka och ta emot meddelanden med IoT-hubben](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguration
I samband med [automatisk enhetskonfiguration](iot-hub-auto-device-config.md), en konfiguration i IoT-hubb definierar önskad konfiguration för en uppsättning enheter twins och ger en uppsättning mått för att rapportera status och förlopp.

## <a name="connection-string"></a>Anslutningssträng
Du kan använda anslutningssträngar i koden app kapsla in informationen som krävs för att ansluta till en slutpunkt. En anslutningssträng innehåller normalt sett adressen för slutpunkten och säkerhetsinformation men anslutningssträngen format variera mellan olika tjänster. Det finns två typer av anslutningssträngen för IoT-hubb-tjänsten:
- *Enheten anslutningssträngar* att enheterna ska ansluta till enheten riktade slutpunkter på en IoT-hubb.
- *IoT-hubb anslutningssträngar* aktivera backend-appar att ansluta till tjänsten riktade slutpunkter på en IoT-hubb.

## <a name="custom-endpoints"></a>Anpassade slutpunkter
Du kan skapa anpassade [slutpunkter](iot-hub-devguide-endpoints.md) på en IoT-hubb för att leverera meddelanden som skickas av en [routningsregel](#routing-rules). Anpassade slutpunkter ansluta direkt till en händelsehubb, en Service Bus-kö eller ett Service Bus-ämne.

## <a name="custom-gateway"></a>Anpassade gateway
Gör att en gateway-anslutning för enheter som inte kan ansluta direkt till [IoT-hubb](#iot-hub). Du kan använda [Azure IoT kant](#azure-iot-edge) att skapa anpassade gateways som implementerar egen kod för att hantera meddelanden, anpassade protokollet konverteringar och annan bearbetning i kanten.

## <a name="data-point-message"></a>Datapunkt meddelande
En datapunkt meddelandet är ett [enhet till moln](#device-to-cloud) meddelande som innehåller [telemetri](#telemetry) data, till exempel vindhastighet eller temperatur.

## <a name="desired-configuration"></a>Önskad konfigurationshantering
I samband med en [enheten dubbla](iot-hub-devguide-device-twins.md), önskad konfiguration refererar till en fullständig uppsättning egenskaper och metadata i enheten dubbla som ska synkroniseras med enheten.

## <a name="desired-properties"></a>Egenskaper
I samband med en [enheten dubbla](iot-hub-devguide-device-twins.md), önskade egenskaper är ett delavsnitt i enheten dubbla som används med [rapporterade egenskaper](#reported-properties) att synkronisera enhetskonfigurationen eller villkor. Egenskaper kan endast anges en [backend-app](#back-end-app) och följas av den [enhetsapp](#device-app).

## <a name="device-to-cloud"></a>Enhet till moln
Refererar till meddelanden som skickas från en ansluten enhet till [IoT-hubb](#iot-hub). Dessa meddelanden kan vara [datapunkt](#data-point-message) eller [interaktiva](#interactive-message) meddelanden. Mer information finns i [skicka och ta emot meddelanden med IoT-hubben](iot-hub-devguide-messaging.md).

## <a name="device"></a>Enhet
En enhet är vanligtvis en småskaliga, fristående datorenhet som kan samla in data eller kontrollera andra enheter i samband med IoT. En enhet kan exempelvis vara en miljön övervakning enhet eller en domänkontrollant för vattning och ventilation systemen i en växthusgaser. Den [enhet katalogen](https://catalog.azureiotsuite.com/) innehåller en lista över maskinvaruenheter som är certifierade för att fungera med [IoT-hubb](#iot-hub).

## <a name="device-app"></a>Enhetsapp
En enhet appen körs på din [enhet](#device) och hanterar kommunikationen med din [IoT-hubb](#iot-hub). Normalt använder du något av de [Azure IoT-enhet SDK](#azure-iot-device-sdks) när du implementerar en enhetsapp. I många av IoT-självstudier, använder du en [simulerade enheten](#simulated-device) i informationssyfte.

## <a name="device-condition"></a>Enhetens tillstånd
Refererar till enheten tillståndsinformation, till exempel anslutningsmetod som används, som rapporteras av en [enhetsapp](#device-app). [Enhetsappar](#device-app) kan också rapportera deras funktioner. Du kan fråga efter villkor och kapaciteten information med hjälp av enheten twins.

## <a name="device-data"></a>Data på enheten
Data på enheten som refererar till per enhet data som lagras i IoT-hubben [identitetsregistret](#identity-registry). Det är möjligt att importera och exportera dessa data.

## <a name="device-explorer"></a>Enheten explorer
Den [enheten explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) är ett verktyg som körs på Windows och gör att du kan hantera dina enheter i den [identitetsregistret](#identity-registry). Verktyget kan också skicka och ta emot meddelanden till dina enheter.

## <a name="device-identities-rest-api"></a>REST API för enhetsidentiteter
Den [enheten identiteter REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) kan du hantera dina enheter som registrerats i den [identitetsregistret](#identity-registry) med hjälp av REST-API. Normalt bör du använda en av de högre [SDK-tjänsten](#azure-iot-service-sdks) som visas i IoT-hubb självstudier.

## <a name="device-identity"></a>Enhetsidentitet
Enhetens identitet är den unika identifieraren som tilldelats varje enhet som registrerats i den [identitetsregistret](#identity-registry).

## <a name="module-identity"></a>Modulen identitet
Modulen identiteten är den unika identifieraren som tilldelats varje modul som hör till en enhet. Modulen identitet också registreras i den [identitetsregistret](#identity-registry).

## <a name="device-management"></a>Enhetshantering
Enhetshantering omfattar hela livscykeln som är associerade med hantering av enheter i IoT-lösningen inklusive planering, etablering, konfigurera, övervaka och borttagning.

## <a name="device-management-patterns"></a>Enhetshanteringsmönster
[IoT-hubb](#iot-hub) aktiverar vanliga device management mönster inklusive startas om, utföra fabriksåterställning och utför uppdateringar av inbyggd programvara på dina enheter.

## <a name="device-messaging-rest-api"></a>REST API för enhetsmeddelanden
Du kan använda den [Device Messaging REST API](https://docs.microsoft.com/rest/api/iothub/httpruntime) från en enhet skickar meddelanden från enhet till moln till en IoT-hubb och tar emot [moln till enhet](#cloud-to-device) meddelanden från en IoT-hubb. Normalt bör du använda en av de högre [enheten SDK](#azure-iot-device-sdks) som visas i IoT-hubb självstudier.

## <a name="device-provisioning"></a>Enhetsetableringen
Enhetsetableringen är en process för att lägga till första [enhetsdata](#device-data) till butiker i din lösning. Om du vill aktivera en ny enhet att ansluta till din hubb, måste du lägga till en enhets-ID och nycklar IoT-hubben [identitetsregistret](#identity-registry). Som en del av etableringsprocessen kan behöva du initiera enhetsspecifika data i andra lösning Arkiv.

## <a name="device-twin"></a>Enhetstvilling
En [enheten dubbla](iot-hub-devguide-device-twins.md) är JSON-dokument som lagrar tillstånd enhetsinformation som metadata, konfigurationer och villkor. [IoT-hubb](#iot-hub) kvarstår en enhet dubbla för varje enhet som du etablerar i din IoT-hubb. Enheten twins gör det möjligt att synkronisera [villkor som enheten](#device-condition) och konfigurationer mellan enheten och lösningen serverdel. Du kan fråga enheten twins att söka efter specifika enheter och fråga status för långvariga åtgärder.

## <a name="module-twin"></a>Modulen dubbla
Precis som dubbla enhet, en modul dubbla är JSON-dokument som lagras modulen tillståndsinformation som metadata, konfigurationer och villkor. IoT-hubb kvarstår en modul dubbla för varje modul-identitet som du etablerar under en enhetsidentitet i din IoT-hubb. Modulen twins kan du synkronisera modulen villkor och konfigurationer mellan modulen och lösningens serverdel. Du kan fråga modulen twins att söka efter specifika moduler och fråga status för långvariga åtgärder.

## <a name="twin-queries"></a>Dubbla frågor
[Enheten och modulen dubbla frågor](iot-hub-devguide-query-language.md) använder frågespråket i SQL-liknande IoT-hubb för att hämta information från din enhet twins eller modulen twins. Du kan använda samma IoT-hubb-frågespråket för att hämta information om [jobb](#job) körs i din IoT-hubb.

## <a name="device-twin-rest-api"></a>Enheten dubbla REST API
Du kan använda den [enheten dubbla REST API](https://docs.microsoft.com/rest/api/iothub/devicetwinapi) från lösningen serverdel för att hantera din enhet twins. API: et kan du hämta och uppdatera [enheten dubbla](#device-twin) egenskaper och anropa [direkt metoder](#direct-method). Normalt bör du använda en av de högre [SDK-tjänsten](#azure-iot-service-sdks) som visas i IoT-hubb självstudier.

## <a name="twin-synchronization"></a>Dubbla synkronisering
Dubbla synkronisering använder den [önskade egenskaper](#desired-properties) i enheten twins eller modulen twins för att konfigurera enheter eller moduler och hämta [rapporterade egenskaper](#reported-properties) från dem ska lagras i dubbla.

## <a name="direct-method"></a>Direkt metod
En [direkt metod](iot-hub-devguide-direct-methods.md) är ett sätt att utlösa en metod som ska köras på en enhet genom att anropa API för din IoT-hubb.

## <a name="endpoint"></a>Slutpunkt
En IoT-hubb visar flera [slutpunkter](iot-hub-devguide-endpoints.md) som gör att dina appar att ansluta till IoT-hubben. Det finns enheten riktade slutpunkter som gör att enheter att utföra åtgärder som skickar [enhet till moln](#device-to-cloud) meddelanden och ta emot [moln till enhet](#cloud-to-device) meddelanden. Finns service-riktade management slutpunkter som möjliggör [backend-appar](#back-end-app) att utföra åtgärder som [enhetsidentitet](#device-identity) hantering och dubbla enhetshantering. Det finns service-riktade [inbyggda slutpunkter](#built-in-endpoints) för att läsa meddelanden från enhet till moln. Du kan skapa [anpassade slutpunkter](#custom-endpoints) att ta emot meddelanden från enhet till moln skickas av en [routningsregel](#routing-rules).

## <a name="event-hubs-service"></a>Tjänsten för Event Hubs
[Händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund. Tjänsten kan du bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. En jämförelse med IoT-hubb-tjänsten finns [jämförelse av Azure IoT Hub och Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Händelsehubb-kompatibel slutpunkt
Att läsa [enhet till moln](#device-to-cloud) meddelanden som skickas till din IoT-hubb du kan ansluta till en slutpunkt på din hubb och använda alla Event Hub-kompatibel-metoden för att läsa dessa meddelanden. Hubb-kompatibel händelsemetoder är med hjälp av den [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) och [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Fältet gateway
En gateway för fältet upprättar anslutningarna för enheter som inte kan ansluta direkt till [IoT-hubb](#iot-hub) och används normalt lokalt med dina enheter. Mer information finns i [vad är Azure IoT Hub?](iot-hub-what-is-iot-hub.md)

## <a name="free-account"></a>Kostnadsfritt konto
Du kan skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/) att slutföra självstudierna IoT-hubb och experimentera med IoT-hubb-tjänsten (och andra Azure-tjänster).

## <a name="gateway"></a>Gateway
Gör att en gateway-anslutning för enheter som inte kan ansluta direkt till [IoT-hubb](#iot-hub). Se även [fältet Gateway](#field-gateway), [molnet Gateway](#cloud-gateway), och [anpassade Gateway](#custom-gateway).

## <a name="identity-registry"></a>Identitetsregistret
Den [identitetsregistret](iot-hub-devguide-identity-registry.md) är den inbyggda komponenten i en IoT-hubb som lagrar information om enskilda enheter som tillåts att ansluta till en IoT-hubb.

## <a name="interactive-message"></a>Interaktiva meddelande
En interaktiv meddelandet är ett [moln till enhet](#cloud-to-device) meddelanden som utlöser en omedelbar åtgärd i lösningens serverdel. Exempelvis kan en enhet skickar ett larm om fel som automatiskt ska loggas en CRM-systemet.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT-Hubbnamnrymd är en helt hanterad Azure-tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka sluta. Mer information finns i [vad är Azure IoT Hub?](iot-hub-what-is-iot-hub.md) Med hjälp av din [Azure-prenumeration](#subscription), kan du skapa IoT-hubbar för att hantera din IoT messaging arbetsbelastningar.

## <a name="iot-hub-metrics"></a>IoT-hubb mått
[IoT-hubb mått](iot-hub-metrics.md) ger data om tillståndet för IoT-hubbar i din [Azure-prenumeration](#subscription). IoT-hubb mått kan du utvärdera den övergripande hälsan för tjänsten och de enheter som är anslutna till den. IoT-hubb mått kan hjälpa dig att se vad som händer med din IoT-hubb och undersöka grundorsaken problem utan att behöva kontakta Azure-supporten.

## <a name="iot-hub-query-language"></a>IoT-hubb frågespråket
Den [IoT-hubb frågespråket](iot-hub-devguide-query-language.md) är en SQL-liknande språk som gör att du kan fråga din [jobb](#job) och twins för enheten.

## <a name="iot-hub-resource-provider-rest-api"></a>IoT-hubb Resursprovidern REST-API
Du kan använda den [IoT Hub Resource Provider REST API](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) att hantera IoT-hubbar i din [Azure-prenumeration](#subscription) utföra åtgärder som att skapa, uppdatera och ta bort hubs.

## <a name="iot-solution-accelerators"></a>IoT solution Accelerator
Azure IoT solution Accelerator paketet tillsammans flera Azure-tjänster i lösningar. Dessa lösningar kan du komma igång snabbt med slutpunkt till slutpunkt-implementeringar av vanliga IoT-scenarier. Mer information finns i [vad är Azure IoT solution Accelerator?](../iot-suite/iot-suite-overview.md)

## <a name="the-iot-extension-for-azure-cli-20"></a>IoT-tillägget för Azure CLI 2.0
[IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) är en plattformsoberoende, kommandorads-verktyget. Verktyget gör det möjligt att hantera dina enheter i den [identitetsregistret](#identity-registry), skicka och motta meddelanden och filer från dina enheter och övervaka dina IoT hub-åtgärder.

## <a name="job"></a>Jobb
Din lösningens serverdel kan använda [jobb](iot-hub-devguide-jobs.md) att schemalägga och spåra aktiviteter i en uppsättning enheter som registrerats med IoT-hubben. Aktiviteter omfattar uppdatera enheten dubbla [önskade egenskaper](#desired-properties), uppdaterar enheten dubbla [taggar](#tags), och anropar [direkt metoder](#direct-method). [IoT-hubb](#iot-hub) använder också jobb till [att importera och exportera](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) från den [identitetsregistret](#identity-registry).

## <a name="jobs-rest-api"></a>Jobb REST-API
Den [jobb REST API](https://docs.microsoft.com/rest/api/iothub/jobapi) kan du hantera [jobb](#job) körs i din IoT-hubb.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) är en av de messaging protokoll som [IoT-hubb](#iot-hub) har stöd för att kommunicera med enheter. Läs mer om meddelanden protokoll som stöds i IoT-hubb [skicka och ta emot meddelanden med IoT-hubben](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Övervakning av åtgärder
IoT-hubb [operations övervakning](iot-hub-operations-monitoring.md) kan du övervaka status för åtgärder för din IoT-hubb i realtid. [IoT-hubb](#iot-hub) spårar händelser över flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier för en IoT-hubb-slutpunkt för bearbetning. Du kan övervaka data för fel eller ställa in mer komplexa bearbetning baserat på datamönster.

## <a name="physical-device"></a>Fysisk enhet
En fysisk enhet är en verklig enhet, till exempel en hallon Pi som ansluter till en IoT-hubb. För enkelhetens skull använder många av IoT-hubb självstudier [simulerade enheter](#simulated-device) så att du kan köra prov på din lokala dator.

## <a name="primary-and-secondary-keys"></a>Primära och sekundära nycklarna
När du ansluter till en enhet riktade eller service-riktade slutpunkt på en IoT-hubb din [anslutningssträngen](#connection-string) innehåller nyckel för att ge dig åtkomst. När du lägger till en enhet för att den [identitetsregistret](#identity-registry) eller Lägg till en [delad åtkomstprincip](#shared-access-policy) till din hubb tjänsten skapar en primär och en sekundär nyckel. Med två nycklar kan du samla från en nyckel till en annan när du uppdaterar en nyckel utan att förlora åtkomsten till IoT-hubben.

## <a name="protocol-gateway"></a>Protocol-gateway
En protocol-gateway distribueras vanligtvis i molnet och tillhandahåller protokollet översättning för enheter som ansluter till [IoT-hubb](#iot-hub). Mer information finns i [vad är Azure IoT Hub?](iot-hub-what-is-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvoter och begränsningar
Det finns olika [kvoter](iot-hub-devguide-quotas-throttling.md) som gäller för din användning av [IoT-hubb](#iot-hub), många av kvoterna variera beroende på nivån av IoT-hubben. [IoT-hubb](#iot-hub) gäller även [begränsar](iot-hub-devguide-quotas-throttling.md) för din användning av tjänsten vid körning.

## <a name="reported-configuration"></a>Rapporterat konfiguration
I samband med en [enheten dubbla](iot-hub-devguide-device-twins.md), rapporterade konfiguration refererar till en fullständig uppsättning egenskaper och metadata i enheten dubbla som ska rapporteras till lösningens serverdel.

## <a name="reported-properties"></a>Rapporterat egenskaper
I samband med en [enheten dubbla](iot-hub-devguide-device-twins.md), rapporterade egenskaper är delar av den enhet dubbla som används med [önskade egenskaper](#desired-properties) att synkronisera enhetskonfigurationen eller villkor. Rapporterat egenskaper kan endast anges den [enhetsapp](#device-app) och kan läsa och efterfrågas av en [backend-app](#back-end-app).

## <a name="resource-group"></a>Resursgrupp
[Azure Resource Manager](#azure-resource-manager) använder resursgrupper för att gruppera relaterade resurser. Du kan använda en resursgrupp för att utföra åtgärder på alla resurser i gruppen samtidigt.

## <a name="retry-policy"></a>Återförsöksprincip
Du hanterar med hjälp av en återförsöksprincip [tillfälliga fel](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) när du ansluter till en tjänst i molnet.

## <a name="routing-rules"></a>Regler för Routning
Du konfigurerar [routningsregler](iot-hub-devguide-messages-read-custom.md) i IoT-hubb för att skicka meddelanden från enhet till moln till ett [inbyggd slutpunkt](#built-in-endpoints) eller [anpassade slutpunkter](#custom-endpoints) för bearbetning av din lösningens serverdel .

## <a name="sasl-plain"></a>SASL OFORMATERAD
SASL OFORMATERAD är ett protokoll som den [AMQP](#advanced-message-queue-protocol) protokoll som används för att överföra säkerhetstoken.

## <a name="shared-access-signature"></a>Signatur för delad åtkomst
Delad åtkomst signaturer (SAS) är en autentiseringsmekanism baserat på SHA-256 säker hashvärden eller URI: er. SAS-autentisering har två komponenter: en _delad åtkomstprincip_ och en _signatur för delad åtkomst_ (kallas ofta för en token). En enhet använder SAS för att autentisera med en IoT-hubb. [Backend-appar](#back-end-app) också använda SAS för att autentisera med tjänsten riktade slutpunkter på en IoT-hubb. Inkluderar vanligtvis, SAS-token i den [anslutningssträngen](#connection-string) att en app använder för att upprätta en anslutning till en IoT-hubb.

## <a name="shared-access-policy"></a>Princip för delad åtkomst
En princip för delad åtkomst definierar behörigheterna för alla som har en giltig [primära och sekundära nycklarna](#primary-and-secondary-keys) som är associerade med principen. Du kan hantera principer för delad åtkomst och nycklar för din hubb i den [portal](#azure-portal).

## <a name="simulated-device"></a>Simulerad enhet
För enkelhetens skull använder många av IoT-hubb självstudier simulerade enheter så att du kan köra prov på din lokala dator. Däremot ett [fysisk enhet](#physical-device) är en verklig enhet, till exempel en hallon Pi som ansluter till en IoT-hubb.

## <a name="solution"></a>Lösning
En _lösning_ kan referera till en Visual Studio-lösning som innehåller ett eller flera projekt. En _lösning_ kan även användas för en IoT-lösning som innehåller element som enheter, [enhetsappar](#device-app), en IoT-hubb, andra Azure-tjänster och [backend-appar](#back-end-app).

## <a name="subscription"></a>Prenumeration
En Azure-prenumeration är om fakturering sker. Varje Azure-resurs som du skapar eller du använder Azure-tjänsten är associerad med en enda prenumeration. Många kvoter gäller även för en prenumeration.

## <a name="system-properties"></a>Systemegenskaper
I samband med en [enheten dubbla](iot-hub-devguide-device-twins.md), Systemegenskaper är skrivskyddad och inkluderar information om enhetsanvändning, till exempel senaste aktivitet tid och anslutningen tillstånd.

## <a name="tags"></a>Taggar
I samband med en [enheten dubbla](iot-hub-devguide-device-twins.md), taggar är enhetens metadata lagras och hämtas av lösningens serverdel i form av ett JSON-dokument. Taggar visas inte för appar på en enhet.

## <a name="telemetry"></a>Telemetri
Enheter samla in telemetridata, till exempel vindhastighet eller temperatur, och använda [datapunkt meddelanden](#data-point-messages) skicka telemetrin till en IoT-hubb.

## <a name="token-service"></a>Tokentjänsten
Du kan använda en tokentjänsten för att implementera en autentiseringsmetod för dina enheter. Den använder en IoT-hubb [delad åtkomstprincip](#shared-access-policy) med **DeviceConnect** behörighet att skapa *enheten omfång* token. Dessa token kan en enhet för att ansluta till din IoT-hubb. En enhet använder en anpassad autentiseringsmetod för att autentisera med tjänsten token. Om enheten har autentiserar, skickar tjänsten token en SAS-token för enheten du använder för att komma åt din IoT-hubb.

## <a name="x509-client-certificate"></a>X.509-klientcertifikat
En enhet kan använda ett X.509-certifikat för att autentisera med [IoT-hubb](#iot-hub). Med hjälp av ett X.509-certifikat är ett alternativ till att använda en [SAS-token](#shared-access-signature).
