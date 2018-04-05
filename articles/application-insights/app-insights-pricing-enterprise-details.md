---
title: Äldre Enterprise plan prisinformation för Azure Application Insights | Microsoft Docs
description: Hantera telemetri volymer och övervaka kostnader i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Enterprise Paketinformation

Application Insights har två prisnivå planer. Standardplanen kallas [grundläggande](app-insights-pricing.md), som innehåller alla samma funktioner som företagsplan på utan ytterligare kostnad och växlar i första hand på mängden data som inhämtas. Om du använder Operations Management Suite, bör du välja företaget plan som har en per nod debiterar tillsammans med dagligen data bidrag och debiteras för data som inhämtas ovan ingår ersättningen.

Finns det [Application Insights sida med priser](http://azure.microsoft.com/pricing/details/application-insights/) för aktuella priser i din valuta och region.

## <a name="heres-how-the-enterprise-plan-works"></a>Här är hur företagsplan fungerar

* Du betalar per nod som skickar telemetri för alla program i Enterprise-abonnemang.
 * En *nod* är en fysisk eller virtuell server-dator eller en plattform som en tjänst-rollinstans som är värd för din app.
 * Datorer för utveckling, klientwebbläsare och mobila enheter räknas inte som noder.
 * Om din app har flera komponenter som skickar telemetri, till exempel en webbtjänst och en serverdel worker räknas de separat.
 * [Live mått dataströmmen](app-insights-live-stream.md) data inte räknas för priser purposes.* över en prenumeration är dina avgifter per nod inte per app. Om du har fem noder skicka telemetri för 12 appar och sedan tillägget är de fem noder.
* Även om avgifter citattecken per månad, använder du debiteras endast för varje timme som en nod skickar telemetri från en app. Varje timme tillägget är inom citattecken månadskostnaden / 744 (antal timmar i 31 dagar i månaden).
* En data-volym tilldelning av 200 MB per dag anges för varje nod som identifierades (med timme). Allokering av oanvända data överförs inte från en dag till nästa.
 * Om du väljer alternativet Enterprise prisnivå får varje prenumeration en daglig ersättning av data baserat på antalet noder som skickas telemetri till Application Insights-resurser i den prenumerationen. Så om du har 5 och skicka data alla dag-noder har en pool ersättning på 1 GB som tillämpas på alla Application Insights-resurser i den prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om en viss dag Application Insights-resurser visas mer data än vad som ingår i den dagliga data allokeringen för den här prenumerationen gäller överförbrukning data-avgifter per GB. 
 * Dagliga data ersättningen beräknas som antalet timmar på dagen (med UTC) att varje nod skickar telemetri divideras med 24 gånger 200 MB. Så om du har 4 noder skicka telemetri under 15 i 24 timmar på dagen, med data för den dagen skulle vara ((4 x 15) / 24) x 200 MB = 500 MB. Till pris 2.30 USD per GB data överförbrukning är tillägget 1,15 USD om noderna skickar 1 GB data den dagen.
 * Företaget planens dagliga ersättningen inte delas med program som du har valt grundläggande alternativet och oanvända ersättning överförs inte från dagliga. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Här följer några exempel för att avgöra antalet distinkta noder

| Scenario                               | Totalt antal per dag nod |
|:---------------------------------------|:----------------:|
| 1 program använder 3 Azure App Service-instanser och 1 virtuell server | 4 |
| 3 program som körs på 2 virtuella datorer och Application Insights-resurser för dessa program finns i samma prenumeration och i Enterprise-plan | 2 | 
| 4 program vars program Insights-resurser finns i samma prenumeration. Alla program körs 2 instanser 16 utanför arbetstid och 4 instanser under 8 användningsnivå. | 13.33 | 
| Molntjänster med 1 Worker-rollen och 1 webbrollen, var 2 instanser körs | 4 | 
| 5-nod Service Fabric-kluster som kör 50 micro-tjänster, varje micro-tjänsten körs 3 instanser | 5|

* Noden exakt inventering beteende beror på som Application Insights SDK ditt program använder. 
  * I SDK-versioner 2.2 och därefter både Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) eller [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) kommer rapporten varje värd för programmet som en nod, till exempel datornamnet för fysiska servrar och värdar för Virtuella datorer eller namnet på instansen för molntjänster.  Det enda undantaget är program endast med hjälp av [.NET Core](https://dotnet.github.io/) och Application Insights Core SDK, där endast en case-nod rapporteras för alla värdar eftersom värdnamn inte är tillgänglig. 
  * För tidigare versioner av SDK, den [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fungerar precis som nyare SDK, men den [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporterar endast en nod oavsett antalet faktiska programvärdar. 
  * Om ditt program använder SDK för att ange roleInstance till ett anpassat värde som standard används samma värde för att fastställa antalet noder. 
  * Om du använder en ny version av SDK med en app som körs från klientdatorer eller mobila enheter, är det möjligt att antalet noder kan returnera ett värde som är mycket stora (från stort antal klientdatorer eller mobilenheter). 
