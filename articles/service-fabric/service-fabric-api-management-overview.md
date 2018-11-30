---
title: Azure Service Fabric med API Management-översikt | Microsoft Docs
description: Den här artikeln är en introduktion till att använda Azure API Management som en gateway till dina Service Fabric-program.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 01b67cc0c20710fcf7c9a072e0ba3baaf286852a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423651"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric med Azure API Management-översikt

Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. I Service Fabric kan en gateway vara valfri tillståndslös tjänst som en [ASP.NET Core-program](service-fabric-reliable-services-communication-aspnetcore.md), eller en annan tjänst för ingångstrafik, till exempel [Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), eller [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Den här artikeln är en introduktion till att använda Azure API Management som en gateway till dina Service Fabric-program. API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning routningsregler och backend-Service Fabric-tjänster. 

## <a name="availability"></a>Tillgänglighet

> [!IMPORTANT]
> Den här funktionen är tillgänglig i den **Premium** och **Developer** nivåerna för API Management på grund av de nödvändiga virtuella nätverks-support.

## <a name="architecture"></a>Arkitektur

En vanlig arkitektur för Service Fabric använder ett enkelsidigt program som gör HTTP-anrop till backend tjänster som exponerar HTTP APIs. Den [Service Fabric komma igång-exempelprogrammet](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) visar ett exempel på den här arkitekturen.

I det här scenariot fungerar en tillståndslös webbtjänst som en gateway till Service Fabric-program. Den här metoden måste du skriva en webbtjänst som kan proxy HTTP-begäranden till backend-tjänster som du ser i följande diagram:

![Service Fabric med Azure API Management topologiöversikt][sf-web-app-stateless-gateway]

Eftersom program växer i komplexitet, så gör gatewayer som måste presentera ett API framför många backend-tjänster. Azure API Management är utformad för att hantera komplexa API: er med regler för routning, åtkomstkontroll, hastighetsbegränsning, övervakning, händelseloggning och cachelagring av svar med minimalt arbete från din sida. Azure API Management har stöd för identifiering av Service Fabric-tjänst, partitionsupplösning och val av replik för att Smart dirigera begäranden direkt till backend-tjänster i Service Fabric, så att du inte behöver skriva en egen tillståndslösa API-gateway. 

I det här scenariot webben Användargränssnittet fortfarande hanteras via en webbtjänst HTTP API-anrop kan hanteras och dirigeras via Azure API Management, enligt följande diagram:

![Service Fabric med Azure API Management topologiöversikt][sf-apim-web-app]

## <a name="application-scenarios"></a>Programscenarier

Tjänster i Service Fabric kan vara antingen tillståndslöst eller tillståndskänsligt och de kan partitioneras med någon av tre scheman: singleton, int-64-intervall och namngivna. Service-slutpunktsmappning kräver identifierar en specifik partition av en specifik tjänstinstans. När du löser en slutpunkt för en tjänst, både service instansnamnet (till exempel `fabric:/myapp/myservice`) samt specifik partition av tjänsten måste anges, utom när det gäller singleton-partitionen.

Azure API Management kan användas med valfri kombination av tillståndslösa tjänster, tillståndskänsliga tjänster och eventuella partitioneringsschema.

## <a name="send-traffic-to-a-stateless-service"></a>Skicka trafik till en tillståndslös tjänst

I det enklaste fallet vidarebefordras trafik till en tillståndslös tjänst-instans. En API Management-åtgärd innehåller en princip för inkommande bearbetning med en Service Fabric för backend-server som mappar till en specifik tillståndslös tjänst-instans i Service Fabric-backend för att uppnå detta. Begäranden som skickas till tjänsten skickas till en slumpmässig replik av tillståndslösa tjänstinstansen.

#### <a name="example"></a>Exempel
I följande scenario ett Service Fabric-program innehåller en tillståndslös tjänst med namnet `fabric:/app/fooservice`, som exponerar ett intern HTTP-API. Namnet på service-instansen är välkänd och kan vara hårdkodade direkt i den inkommande bearbetning för API Management-principen. 

![Service Fabric med Azure API Management topologiöversikt][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Skicka trafik till en tillståndskänslig tjänst

Liknar tillståndslös tjänst-scenariot, trafik kan vidarebefordras till en tillståndskänslig tjänst-instans. I det här fallet en API Management-åtgärd innehåller en princip för inkommande bearbetning med en Service Fabric för backend-server som mappar en begäran till en specifik partition för en specifik *tillståndskänslig* tjänstinstans. Partition att mappa varje begäran till beräknas via en lambda-metod med vissa indata från inkommande HTTP-begäran, till exempel ett värde i URL-sökvägen. Principen kan konfigureras för att skicka begäranden till den primära repliken eller till en slumpmässig replik för läsåtgärder.

#### <a name="example"></a>Exempel

I följande scenario ett Service Fabric-program innehåller en partitionerad tillståndskänslig tjänst med namnet `fabric:/app/userservice` som exponerar ett intern HTTP-API. Namnet på service-instansen är välkänd och kan vara hårdkodade direkt i den inkommande bearbetning för API Management-principen.  

Tjänsten är partitionerad med Int64 partitionsschema med två partitioner och ett nyckelintervall som sträcker sig över `Int64.MinValue` till `Int64.MaxValue`. Backend-principen beräknar en partitionsnyckel inom intervallet genom att konvertera den `id` värdet som angetts i URL-sökvägen för begäran till en 64-bitars heltal, även om alla algoritmer kan användas här för att beräkna Partitionsnyckeln. 

![Service Fabric med Azure API Management topologiöversikt][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Skicka trafik till flera tillståndslösa tjänster

Du kan definiera en API Management-åtgärd som mappar begäranden till fler än en tjänstinstans i mer avancerade scenarier. I det här fallet innehåller varje åtgärd en princip som mappar begäranden till en specifik tjänstinstans baserat på värden från den inkommande HTTP-begäran, till exempel URL-sökvägen eller frågesträngen, och för tillståndskänsliga tjänster, en partition inom tjänstinstansen. 

För att uppnå innehåller en API Management-åtgärd en princip för inkommande bearbetning med en Service Fabric för backend-server som mappar till en tillståndslös tjänst-instans i Service Fabric serverdelen baserat på värden som hämtats från den inkommande HTTP-begäran. Begäranden till en tjänstinstans skickas till en slumpmässig replik av tjänstinstansen.

#### <a name="example"></a>Exempel

I det här exemplet skapas en ny tillståndslös tjänst-instans för varje användare i ett program med ett dynamiskt genererat namn med hjälp av följande formel:
 
 - `fabric:/app/users/<username>`

 Varje tjänst har ett unikt namn, men de är inte känt direkta eftersom tjänsterna skapas som svar på användaren eller administratören indata och därför kan inte vara hårdkodade i APIM-principer eller regler för routning. I stället namnet på tjänsten som du vill skicka en begäran skapas i backend-principdefinitionen från den `name` värdet som angetts i URL-sökvägen för begäran. Exempel:

  - En begäran om att `/api/users/foo` dirigeras till tjänstinstans `fabric:/app/users/foo`
  - En begäran om att `/api/users/bar` dirigeras till tjänstinstans `fabric:/app/users/bar`

![Service Fabric med Azure API Management topologiöversikt][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Skicka trafik till flera tillståndskänsliga tjänster

Liknar exempel tillståndslös tjänst, kan en API Management-åtgärd mappa begäranden till fler än en **tillståndskänslig** tjänsten instans, i vilket fall du även behöva utföra partitionsupplösning för varje tillståndskänslig tjänst-instans.

För att uppnå innehåller en API Management-åtgärd en princip för inkommande bearbetning med en Service Fabric för backend-server som mappar till en tillståndskänslig tjänst-instans i Service Fabric serverdelen baserat på värden som hämtats från den inkommande HTTP-begäran. Förutom att mappa en begäran till specifik tjänstinstans, kan förfrågan också mappas till en specifik partition inom tjänstinstansen och eventuellt till den primära repliken eller en slumpmässig sekundär replik i partitionen.

#### <a name="example"></a>Exempel

I det här exemplet skapas en ny tillståndskänslig tjänst-instans för varje användare av programmet med ett dynamiskt genererat namn med hjälp av följande formel:
 
 - `fabric:/app/users/<username>`

 Varje tjänst har ett unikt namn, men de är inte känt direkta eftersom tjänsterna skapas som svar på användaren eller administratören indata och därför kan inte vara hårdkodade i APIM-principer eller regler för routning. I stället namnet på tjänsten som du vill skicka en begäran skapas i backend-principdefinitionen från den `name` tillhandahålla värde URL-sökvägen för begäran. Exempel:

  - En begäran om att `/api/users/foo` dirigeras till tjänstinstans `fabric:/app/users/foo`
  - En begäran om att `/api/users/bar` dirigeras till tjänstinstans `fabric:/app/users/bar`

Varje tjänstinstans är också partitionerad med Int64 partitionsschema med två partitioner och ett nyckelintervall som sträcker sig över `Int64.MinValue` till `Int64.MaxValue`. Backend-principen beräknar en partitionsnyckel inom intervallet genom att konvertera den `id` värdet som angetts i URL-sökvägen för begäran till en 64-bitars heltal, även om alla algoritmer kan användas här för att beräkna Partitionsnyckeln. 

![Service Fabric med Azure API Management topologiöversikt][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Nästa steg

Följ den [självstudien](service-fabric-tutorial-deploy-api-management.md) att konfigurera ditt första Service Fabric-kluster med API Management och flow förfrågningar via API Management till dina tjänster.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png