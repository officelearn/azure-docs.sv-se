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
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: 65307eab0bf1b5f502f11c14c369826cd12e0966
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309836"
---
# <a name="enterprise-plan-details"></a>Information om Enterprise-prenumerationsavtal

Azure Application Insights har två prisnivå planer: Basic och Enterprise. Den [grundläggande](app-insights-pricing.md) priser plan är standardplanen. Den innehåller alla plan företagsfunktioner, utan extra kostnad. De grundläggande planen växlar huvudsakligen på mängden data som inhämtas är. 

Enterprise-planen har en avgift per nod och varje nod som tar emot en dagliga data ersättning. I företaget debiteras priser plan, du för data som inhämtas ovan ingår ersättningen. Om du använder Operations Management Suite, bör du välja Enterprise planen. 

Aktuella priser i din valuta och region, se [priser för Application Insights](http://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> I April 2018 vi [introduceras](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) en ny prissättningsmodell för övervakning av Azure. Den här modellen antar en enkel ”betalning per användning” modell över komplett portfölj av övervaka tjänster. Lär dig mer om den [nya Prismodell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hur till [utvärdera effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) baserat på ditt användningsmönster och [så att använda den nya modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>Så här fungerar företagsplan

* Du betalar för varje nod som skickar telemetri för alla program i Enterprise-abonnemang.
 * En *nod* är en fysisk eller virtuell server-dator eller en plattform som en tjänst rollinstans som är värd för din app.
 * Räknas inte som noder Development datorer, klientwebbläsare och mobila enheter.
 * Om din app har flera komponenter som skickar telemetri, till exempel en webbtjänst och en serverdel worker räknas komponenterna separat.
 * [Live mått dataströmmen](app-insights-live-stream.md) data inte räknas för prissättning. I en prenumeration som är dina avgifter per nod inte per app. Om du har fem noder som skicka telemetri för 12 appar, tillägget är för fem noder.
* Även om avgifter citattecken per månad, använder du debiteras endast för varje timme som en nod skickar telemetri från en app. Timvisa kostnad är den inom citattecken månadskostnaden dividerat med 744 (antal timmar i 31 dagar i månaden).
* En data-volym tilldelning av 200 MB per dag anges för varje nod som har identifierats (med timme). Allokering av oanvända data överförs inte från en dag till nästa.
 * Om du väljer företaget priser plan för får varje prenumeration en daglig ersättning av data baserat på antalet noder som skickar telemetri i Application Insights-resurser i den prenumerationen. Så om du har fem noder som skickar data hela dagen, har du en ersättning som är grupperade 1 GB som tillämpas på alla Application Insights-resurser i den prenumerationen. Det spelar ingen roll om vissa noder skicka mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om en viss dag Application Insights-resurser visas mer data än vad som ingår i den dagliga data allokeringen för den här prenumerationen gäller överförbrukning data-avgifter per GB. 
 * Dagliga data ersättningen beräknas som antalet timmar på dagen (med UTC) att varje nod skickar telemetri divideras med 24 multiplicerat med 200 MB. Så om du har fyra noder som skicka telemetri under 15 i 24 timmar på dagen, med data för den dagen skulle vara ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Till pris 2.30 USD per GB data överförbrukning är tillägget 1,15 USD om noderna skickar 1 GB data den dagen.
 * Enterprise-plan dagliga ersättningen delas inte med program som du har valt den grundläggande planen. Oanvända ersättningen överförts inte från dagliga. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Exempel på hur du fastställer antalet distinkta noder

| Scenario                               | Totalt antal per dag nod |
|:---------------------------------------|:----------------:|
| 1 program med 3 Azure App Service-instanser och 1 virtuell server | 4 |
| 3 program som körs på 2 virtuella datorer; Application Insights-resurser för dessa program finns i samma prenumeration och i Enterprise-plan | 2 | 
| 4 program vars program Insights-resurser finns i samma prenumeration; varje program som körs under lågtrafik för 16-2 instanser och 4 instanser under 8 användningsnivå | 13.33 | 
| Molntjänster med 1 Worker-rollen och 1 webbrollen, var 2 instanser körs | 4 | 
| Ett 5-nod Azure Service Fabric-kluster som kör 50 mikrotjänster; varje mikrotjänster 3 instanser körs | 5|

* Exakt nod inventeringen beror på vilken Application Insights SDK använder ditt program. 
  * I SDK versioner 2.2 eller senare, både Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) och [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) rapportera varje värd för programmet som en nod. Exempel är datornamnet för fysiska servrar och värdar för Virtuella datorer eller namnet på instansen för molntjänster.  Det enda undantaget är ett program som använder bara den [.NET Core](https://dotnet.github.io/) och Application Insights Core SDK. I så fall rapporteras endast en nod för alla värdar eftersom värdnamn är inte tillgänglig. 
  * För tidigare versioner av SDK, den [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fungerar som nyare SDK, men [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporterar endast en nod, oavsett antalet värdar för programmet. 
  * Om programmet använder SDK för att ange **roleInstance** till ett anpassat värde som standard samma värde används för att fastställa antalet noder. 
  * Om du använder en ny version av SDK med en app som körs från klientdatorer eller mobilenheter kan antalet noder returnera ett värde som är mycket stora (på grund av stort antal klientdatorer eller mobilenheter). 
