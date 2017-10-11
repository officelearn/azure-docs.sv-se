---
title: "Azure Service Fabric API Management översikt | Microsoft Docs"
description: "Den här artikeln är en introduktion till Azure API Management som en gateway till Service Fabric-program."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: a3eedacac5efb53f82e46a56285713dece56ffe8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric med översikt över Azure API Management

Molnprogram måste vanligtvis en frontend-gateway att tillhandahålla en enda åtkomstpunkt för inkommande trafik för användare, enheter eller andra program. I Service Fabric en gateway kan alla tillståndslösa tjänster som en [ASP.NET Core programmet](service-fabric-reliable-services-communication-aspnetcore.md), eller en annan tjänst som utformats för trafik ingång [Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/), [IoT-hubb](https://docs.microsoft.com/azure/iot-hub/), eller [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Den här artikeln är en introduktion till Azure API Management som en gateway till Service Fabric-program. API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning regler för routning till backend-Service Fabric-tjänster. 

## <a name="architecture"></a>Arkitektur
En gemensam Service Fabric-arkitektur använder en enda sida webbprogram som anropar HTTP backend-tjänster som exponerar HTTP APIs. Den [Service Fabric-igång exempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) visar ett exempel på den här arkitekturen.

I det här scenariot fungerar en tillståndslös webbtjänst som gateway till Service Fabric-programmet. Den här metoden kräver att skriva en webbtjänst som kan proxy HTTP-begäranden till backend-tjänster som visas i följande diagram:

![Service Fabric med översikt över Azure API Management-topologi][sf-web-app-stateless-gateway]

Eftersom program växer komplexitet, så gör gateways som måste presentera ett API framför myriad backend-tjänster. Azure API Management är utformat för att hantera komplexa API: er med regler för routning, åtkomstkontroll, hastighetsbegränsning, övervakning, loggning och svar cachelagring med minimalt arbete från din sida. Azure API Management har stöd för identifiering av Service Fabric-tjänst, partition upplösning och val av replik för att dirigera Intelligent begäranden direkt till backend-tjänster i Service Fabric så att du inte behöver skriva tillståndslös API-gatewayen. 

I det här scenariot webben UI fortfarande hanteras via en webbtjänst när HTTP-API-anrop hanteras och dirigeras via Azure API Management som visas i följande diagram:

![Service Fabric med översikt över Azure API Management-topologi][sf-apim-web-app]

## <a name="application-scenarios"></a>Programscenarier

Tjänster i Service Fabric kan vara tillståndslösa och tillståndskänsliga och de kan partitioneras med någon av tre scheman: singleton, int-64-intervall och namngivna. Tjänsten slutpunktsmappning kräver identifierar en specifik partition för en specifik tjänst-instans. Vid matchning av en slutpunkt av en tjänst, både service instansnamnet (till exempel `fabric:/myapp/myservice`) samt viss partition i tjänsten måste anges, med undantag för singleton-partitionen.

Azure API Management kan användas med valfri kombination av tillståndslösa tjänster, tillståndskänsliga tjänster och eventuella partitioneringsschema.

## <a name="send-traffic-to-a-stateless-service"></a>Skicka trafik till en tillståndslös tjänst

I det enklaste fallet vidarebefordras trafik till en instans av tillståndslösa tjänsten. För att uppnå innehåller en API Management-åtgärd en princip för inkommande bearbetning med ett Service Fabric-backend som mappar till en specifik tillståndslös tjänstinstans i Service Fabric-backend. Förfrågningar som skickas till tjänsten skickas till en slumpmässig replik av tillståndslösa tjänstinstansen.

#### <a name="example"></a>Exempel
I följande scenario ett Service Fabric-program innehåller en tillståndslös med namnet `fabric:/app/fooservice`, som Exponerar en intern HTTP-API. Namnet på instansen är kända och kan vara hårdkodade direkt i principen för API Management inkommande bearbetning. 

![Service Fabric med översikt över Azure API Management-topologi][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Skicka trafik till en tillståndskänslig service

Liknar tillståndslösa tjänsten scenario, trafik ska kunna vidarebefordras till en tillståndskänslig service-instans. I det här fallet en API Management-åtgärd innehåller en princip för inkommande bearbetning med ett Service Fabric-backend som mappar en begäran till en specifik partition för en specifik *stateful* tjänstinstansen. Partitionen att mappa varje begäran att beräknas via ett lambda-metod som använder indata från inkommande HTTP-begäran, till exempel ett värde i en URL-sökväg. Principen kan konfigureras för att skicka begäranden till den primära repliken eller till en slumpmässig replik för läsåtgärder.

#### <a name="example"></a>Exempel

I följande scenario ett Service Fabric-program innehåller en partitionerad tillståndskänslig med namnet `fabric:/app/userservice` som Exponerar en intern HTTP-API. Namnet på instansen är kända och kan vara hårdkodade direkt i principen för API Management inkommande bearbetning.  

Tjänsten är partitionerad med två partitioner och en nyckel intervall som sträcker sig över Int64 partitionsschemat `Int64.MinValue` till `Int64.MaxValue`. Backend-principen beräknar en partitionsnyckel inom det intervallet genom att konvertera den `id` värden som anges i URL-begäran-sökvägen till en 64-bitars heltal, även om en algoritm kan användas här för att beräkna Partitionsnyckeln. 

![Service Fabric med översikt över Azure API Management-topologi][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Skicka trafik till flera tillståndslösa tjänster

Du kan definiera en API Management-åtgärd som mappar begäranden till mer än en tjänstinstans i mer avancerade scenarier. I det här fallet innehåller varje åtgärd en princip som mappar begäranden till en specifik tjänst instanser baserade på värden från den inkommande HTTP-begäranden, till exempel URL-sträng för sökväg eller fråga och för tillståndskänsliga tjänster, en partition i tjänstinstansen. 

För att uppnå innehåller en API Management-åtgärd en princip för inkommande bearbetning med ett Service Fabric-backend som mappar till en instans av tillståndslösa tjänsten i Service Fabric serverdel baserat på värden som hämtats från den inkommande HTTP-begäranden. Begäranden till en instans av tjänsten skickas till en slumpmässig replik av tjänstinstansen.

#### <a name="example"></a>Exempel

I det här exemplet skapas en ny instans av tillståndslösa tjänsten för varje användare i ett program med ett dynamiskt genererat namn med hjälp av följande formel:
 
 - `fabric:/app/users/<username>`

 Varje tjänst har ett unikt namn, men de är inte känt direkta eftersom tjänsterna skapas som svar på användaren eller administratören indata och därför kan inte vara hårdkodat i APIM principer eller regler för routning. I stället namnet på den tjänst som du vill skicka en begäran skapas i backend-principdefinitionen från den `name` värden som anges i URL-begäran-sökväg. Exempel:

  - En begäran om att `/api/users/foo` dirigeras till tjänstinstans`fabric:/app/users/foo`
  - En begäran om att `/api/users/bar` dirigeras till tjänstinstans`fabric:/app/users/bar`

![Service Fabric med översikt över Azure API Management-topologi][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Skicka trafik till flera tillståndskänsliga tjänster

Liknar tillståndslösa tjänsten exempel, en API Management-åtgärd kan du mappa begäranden till fler än en **stateful** tjänsten instans kan då du även behöva utföra partition matchning för varje tillståndskänslig service-instans.

För att uppnå innehåller en API Management-åtgärd en princip för inkommande bearbetning med ett Service Fabric-backend som mappar till en tillståndskänslig service-instans i Service Fabric serverdel baserat på värden som hämtats från den inkommande HTTP-begäranden. Förutom att mappa en begäran till specifika tjänstinstans, kan begäran också mappas till en specifik partition inom tjänstinstansen och eventuellt den primära repliken eller en slumpmässig sekundär replik i partitionen.

#### <a name="example"></a>Exempel

I det här exemplet skapas en ny tillståndskänslig service-instans för varje användare av programmet med ett dynamiskt genererat namn med hjälp av följande formel:
 
 - `fabric:/app/users/<username>`

 Varje tjänst har ett unikt namn, men de är inte känt direkta eftersom tjänsterna skapas som svar på användaren eller administratören indata och därför kan inte vara hårdkodat i APIM principer eller regler för routning. I stället namnet på den tjänst som du vill skicka en begäran skapas i backend-principdefinitionen från den `name` värdet som en URL-sökväg för begäran. Exempel:

  - En begäran om att `/api/users/foo` dirigeras till tjänstinstans`fabric:/app/users/foo`
  - En begäran om att `/api/users/bar` dirigeras till tjänstinstans`fabric:/app/users/bar`

Varje tjänstinstans är också partitionerad använder Int64 partitionsschema med två partitioner och flera nycklar som omfattar `Int64.MinValue` till `Int64.MaxValue`. Backend-principen beräknar en partitionsnyckel inom det intervallet genom att konvertera den `id` värden som anges i URL-begäran-sökvägen till en 64-bitars heltal, även om en algoritm kan användas här för att beräkna Partitionsnyckeln. 

![Service Fabric med översikt över Azure API Management-topologi][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Nästa steg

Följ den [snabbstartsguiden](service-fabric-api-management-quick-start.md) att ställa in din första Service Fabric-kluster med API-hantering och flödar förfrågningar via API-hantering till dina tjänster.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png