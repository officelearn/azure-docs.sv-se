---
title: Översikt över Azure Service Fabric med API Management
description: Den här artikeln är en introduktion till hur du använder Azure API Management som en gateway till dina Service Fabric-program.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028548"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric med Azure API Management-översikt

Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. I Service Fabric kan en gateway vara vilken tillståndslös tjänst som helst, till exempel ett [ASP.NET Core-program](service-fabric-reliable-services-communication-aspnetcore.md)eller en annan tjänst som är avsedd för trafikinträngning, till exempel [Event Hubs,](https://docs.microsoft.com/azure/event-hubs/) [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)eller Azure API [Management](https://docs.microsoft.com/azure/api-management/).

Den här artikeln är en introduktion till hur du använder Azure API Management som en gateway till dina Service Fabric-program. API Management integreras direkt med Service Fabric, så att du kan publicera API:er med en omfattande uppsättning routningsregler till dina backend-Tjänst Fabric-tjänster.

## <a name="availability"></a>Tillgänglighet

> [!IMPORTANT]
> Den här funktionen är tillgänglig på **nivåerna Premium** och **utvecklare** i API Management på grund av den nödvändiga supporten för virtuella nätverk.

## <a name="architecture"></a>Arkitektur

En vanlig Service Fabric-arkitektur använder ett ensidigt webbprogram som gör HTTP-anrop till backend-tjänster som exponerar HTTP-API:er. Kom [igång-exempelprogrammet Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) visar ett exempel på den här arkitekturen.

I det här fallet fungerar en tillståndslös webbtjänst som gateway till Programmet Service Fabric. Den här metoden kräver att du skriver en webbtjänst som kan proxy HTTP-begäranden till backend-tjänster, som visas i följande diagram:

![Service Fabric med Azure API Management-topologi översikt][sf-web-app-stateless-gateway]

När program växer i komplexitet, så gör gateways som måste presentera ett API framför otaliga backend-tjänster. Azure API Management är utformat för att hantera komplexa API:er med routningsregler, åtkomstkontroll, hastighetsbegränsning, övervakning, händelseloggning och svarscachelagring med minimalt arbete från din sida. Azure API Management stöder identifiering av tjänstidentifiering, partitionsupplösning och replikval för att på ett intelligent sätt dirigera begäranden direkt till backend-tjänster i Service Fabric så att du inte behöver skriva din egen tillståndslösa API-gateway. 

I det här fallet visas webbgränssnittet fortfarande via en webbtjänst, medan HTTP API-anrop hanteras och dirigeras via Azure API Management, vilket visas i följande diagram:

![Service Fabric med Azure API Management-topologi översikt][sf-apim-web-app]

## <a name="application-scenarios"></a>Programscenarier

Tjänster i Service Fabric kan vara antingen tillståndslösa eller tillståndskänsliga, och de kan delas med ett av tre scheman: singleton, int-64-intervall och namnges. Tjänstslutpunktsmatchning kräver identifiering av en specifik partition för en viss tjänstinstans. När du löser en slutpunkt för en tjänst måste `fabric:/myapp/myservice`både tjänstinstansnamnet (till exempel) och den specifika partitionen för tjänsten anges, utom när det gäller singleton-partition.

Azure API Management kan användas med valfri kombination av tillståndslösa tjänster, tillståndskänsliga tjänster och alla partitioneringsscheman.

## <a name="send-traffic-to-a-stateless-service"></a>Skicka trafik till en tillståndslös tjänst

I det enklaste fallet vidarebefordras trafiken till en tillståndslös tjänstinstans. För att uppnå detta innehåller en API Management-åtgärd en inkommande bearbetningsprincip med en service fabric-serverdel som mappar till en viss tillståndslös tjänstinstans i serverdelen Service Fabric. Begäranden som skickas till den tjänsten skickas till en slumpmässig instans av tjänsten.

**Exempel**

I följande scenario innehåller ett Service Fabric-program `fabric:/app/fooservice`en tillståndslös tjänst med namnet , som exponerar ett internt HTTP-API. Tjänstinstansnamnet är välkänt och kan hårdkodas direkt i API Management-inkommande bearbetningsprincipen. 

![Service Fabric med Azure API Management-topologi översikt][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Skicka trafik till en tillståndskänslig tjänst

I likhet med det tillståndslösa tjänstscenariot kan trafik vidarebefordras till en tillståndskänslig tjänstinstans. I det här fallet innehåller en API Management-åtgärd en inkommande bearbetningsprincip med en service fabric-serverdel som mappar en begäran till en viss partition med en viss *tillståndskänslig* tjänstinstans. Partitionen som ska mappas varje begäran till beräknas via en lambda-metod med hjälp av viss indata från den inkommande HTTP-begäran, till exempel ett värde i URL-sökvägen. Principen kan konfigureras för att skicka begäranden till den primära repliken eller till en slumpmässig replik för läsåtgärder.

**Exempel**

I följande scenario innehåller ett Service Fabric-program en `fabric:/app/userservice` partitionerad tillståndskänslig tjänst med namnet som exponerar ett internt HTTP-API. Tjänstinstansnamnet är välkänt och kan hårdkodas direkt i API Management-inkommande bearbetningsprincipen.  

Tjänsten partitioneras med int64-partitionsschemat med två partitioner `Int64.MinValue` och `Int64.MaxValue`ett nyckelintervall som sträcker sig till . Backend-principen beräknar en partitionsnyckel inom `id` det intervallet genom att konvertera värdet i url-begäranden till ett 64-bitars heltal, även om alla algoritmer kan användas här för att beräkna partitionsnyckeln. 

![Service Fabric med Azure API Management-topologi översikt][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Skicka trafik till flera tillståndslösa tjänster

I mer avancerade scenarier kan du definiera en API Management-åtgärd som mappar begäranden till mer än en tjänstinstans. I det här fallet innehåller varje åtgärd en princip som mappar begäranden till en viss tjänstinstans baserat på värden från den inkommande HTTP-begäran, till exempel URL-sökvägen eller frågesträngen, och när det gäller tillståndskänsliga tjänster, en partition i tjänstinstansen.

För att uppnå detta innehåller en API Management-åtgärd en inkommande bearbetningsprincip med en service fabric-serverdel som mappar till en tillståndslös tjänstinstans i service fabric-serverdelen baserat på värden som hämtats från den inkommande HTTP-begäran. Begäranden till en tjänst skickas till en slumpmässig instans av tjänsten.

**Exempel**

I det här exemplet skapas en ny tillståndslös tjänstinstans för varje användare av ett program med ett dynamiskt genererat namn med hjälp av följande formel:

- `fabric:/app/users/<username>`

  Varje tjänst har ett unikt namn, men namnen är inte kända på framsidan eftersom tjänsterna skapas som svar på använda- eller administratörsindata och därför inte kan hårdkodas till APIM-principer eller routningsregler. I stället genereras namnet på den tjänst som en begäran ska skickas `name` till i principdefinitionen för backend-principen från värdet i sökvägen för URL-begäran. Ett exempel:

  - En begäran `/api/users/foo` dirigeras till serviceinstansen`fabric:/app/users/foo`
  - En begäran `/api/users/bar` dirigeras till serviceinstansen`fabric:/app/users/bar`

![Service Fabric med Azure API Management-topologi översikt][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Skicka trafik till flera tillståndskänsliga tjänster

I likhet med det tillståndslösa tjänstexemplet kan en API Management-åtgärd mappa begäranden till mer än en **tillståndskänslig** tjänstinstans, i vilket fall du också kan behöva utföra partitionslösning för varje tillståndskänslig tjänstinstans.

För att uppnå detta innehåller en API Management-åtgärd en inkommande bearbetningsprincip med en service fabric-serverdel som mappar till en tillståndskänslig tjänstinstans i service fabric-serverdelen baserat på värden som hämtats från den inkommande HTTP-begäran. Förutom att mappa en begäran till specifik tjänstinstans kan begäran också mappas till en specifik partition i tjänstinstansen och eventuellt till antingen den primära repliken eller en slumpmässig sekundär replik i partitionen.

**Exempel**

I det här exemplet skapas en ny tillståndskänslig tjänstinstans för varje användare av programmet med ett dynamiskt genererat namn med hjälp av följande formel:

- `fabric:/app/users/<username>`

  Varje tjänst har ett unikt namn, men namnen är inte kända på framsidan eftersom tjänsterna skapas som svar på använda- eller administratörsindata och därför inte kan hårdkodas till APIM-principer eller routningsregler. I stället genereras namnet på den tjänst som en begäran ska skickas `name` till i principdefinitionen för backend-principen från det värde som angav sökvägen till URL-begäran. Ett exempel:

  - En begäran `/api/users/foo` dirigeras till serviceinstansen`fabric:/app/users/foo`
  - En begäran `/api/users/bar` dirigeras till serviceinstansen`fabric:/app/users/bar`

Varje tjänstinstans partitioneras också med int64-partitionsschemat med `Int64.MinValue` två `Int64.MaxValue`partitioner och ett nyckelintervall som sträcker sig till . Backend-principen beräknar en partitionsnyckel inom `id` det intervallet genom att konvertera värdet i url-begäranden till ett 64-bitars heltal, även om alla algoritmer kan användas här för att beräkna partitionsnyckeln. 

![Service Fabric med Azure API Management-topologi översikt][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Nästa steg

Följ [självstudien](service-fabric-tutorial-deploy-api-management.md) för att konfigurera ditt första Service Fabric-kluster med API Management och flödesbegäranden via API Management till dina tjänster.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
