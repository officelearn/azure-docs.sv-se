---
title: Äldre Enterprise plan prisinformation för Azure Application Insights | Microsoft Docs
description: Hantera volymer för telemetri och övervaka kostnader i Application Insights.
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
ms.openlocfilehash: b2a93c7d3b512a34ab5d2e4fd020415739466c2e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235300"
---
# <a name="enterprise-plan-details"></a>Information om Enterprise-prenumerationsavtal

Azure Application Insights har två prisavtalen: Basic och Enterprise. Den [grundläggande](app-insights-pricing.md) prisavtal är standard-prenumerationen. Den innehåller alla plan företagsfunktioner, utan extra kostnad. Basic-avtalet fakturor främst på mängden data som matas in. 

Enterprise-avtalet har en avgift per nod, och varje nod som tar emot en dagliga datakvoten. I företaget debiteras prisavtal, du för data som matas in ovan ingår tilldelningen. Om du använder Operations Management Suite, bör du välja Enterprise-avtalet. 

Löpande priser i din valuta och region, se [priser för Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> I April 2018 kommer vi [introduceras](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) en ny Prismodell för Azure-övervakning. Den här modellen antar en enkel modell för ”betala per användning” i den fullständiga portföljen av övervakningstjänster. Läs mer om den [nya prissättningsmodellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hur till [utvärdera effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) baserat på dina användningsmönster och [så här att den nya modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>Så här fungerar företagsplanen

* Du betalar för varje nod som skickar telemetri för alla appar i Enterprise-avtalet.
 * En *noden* är en fysisk eller virtuell server-dator eller en platform-as-a-service-rollinstans som är värd för din app.
 * Utveckling av datorer, klientwebbläsare och mobila enheter räknas inte som noder.
 * Om din app har flera komponenter som skickar telemetri, till exempel en webbtjänst och en serverarbetsroll räknas komponenterna separat.
 * [Live Metrics Stream](app-insights-live-stream.md) data inte räknas för prissättning. I en prenumeration är en av dina avgifter per nod, inte per app. Om du har fem noder som skickar telemetri för 12 appar, avgiften gäller för fem noder.
* Även om avgifter anges per månad, debiteras du bara för en timme som en nod skickar telemetri från en app. Per timme avgiften gäller den citerade månadskostnaden divideras med 744 (antal timmar under en månad som 31 dagar).
* En data-volymfördelning på 200 MB per dag får för varje nod som har identifierats (med per timme kornighet). Oanvända dataallokeringen är inte överföras mellan en dag till nästa.
 * Om du väljer Enterprise prisavtal får varje prenumeration en daglig datakvot baserat på antalet noder som skickar telemetri till Application Insights-resurser i prenumerationen. Så om du har fem noder som skickar data hela dagen, får du en datapool på 1 GB som tillämpas på alla Application Insights-resurser i prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om en viss dag får Application Insights-resurser mer data än vad som ingår i allokeringen av dagliga data för den här prenumerationen, gäller priserna för per GB extra data. 
 * Den dagliga datakvoten beräknas som antalet timmar under dagen (med UTC) att varje nod skickar telemetri dividerat med 24 multiplicerat med 200 MB. Så om du har fyra noder som skickar telemetri under 15 i 24 timmar under dagen inkluderade data för den dagen skulle vara ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Till samma pris 2,30 USD per GB för överförbrukning är avgiften 1,15 USD om noderna som skickar 1 GB data den dagen.
 * Enterprise-plan dagliga kvoten delas inte med program som du har valt Basic-avtal. Oanvända tilldelningen är inte överföras från dag till dag. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Exempel på hur du fastställer distinkta nodantal

| Scenario                               | Totalt antal dagliga nodantal |
|:---------------------------------------|:----------------:|
| 1 program med 3 Azure App Service-instanser och 1 virtuell server | 4 |
| 3 program som körs på 2 virtuella datorer; Application Insights-resurser för dessa program finns i samma prenumeration och i Enterprise-avtalet | 2 | 
| 4 program vars program Insights-resurser finns i samma prenumeration; varje program som körs 2 instanser vid 16 låg belastning och 4 instanser under 8 belastning | 13.33 | 
| Cloud services med 1 Worker-roll och 1 Webbroll, vart och ett körs 2 instanser | 4 | 
| 5-nods Azure Service Fabric-kluster som kör 50 mikrotjänster; varje mikrotjänst 3 instanser körs | 5|

* Exakt noden inventeringen beror på vilken Application Insights SDK använder ditt program. 
  * I SDK-versioner 2.2 eller senare, både Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) och [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) rapportera varje värd för programmet som en nod. Exempel är datornamnet för fysisk server och VM-värdar eller instansnamnet för molntjänster.  Det enda undantaget är ett program som använder bara den [.NET Core](https://dotnet.github.io/) och Application Insights Core SDK. I så fall kan rapporteras endast en nod för alla värdar eftersom värdnamnet inte är tillgängligt. 
  * För tidigare versioner av SDK, den [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fungerar som de nyare SDK-versionerna, men [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporterar endast en nod, oavsett antalet värdar för programmet. 
  * Om ditt program använder SDK för att ange **Rollinstans** till ett anpassat värde som standard samma värde används för att bestämma antalet noder. 
  * Om du använder en ny SDK-version med en app som körs från klientdatorer eller mobilenheter, kanske antalet noder returnerar ett tal som är mycket stor (på grund av det stora antalet klientdatorer eller mobilenheter). 
