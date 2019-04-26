---
title: Hantera användning och kostnader för Azure Application Insights | Microsoft Docs
description: Hantera volymer för telemetri och övervaka kostnader i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: edf724d6fd659ad4e8887a9c68467d17a33f5ccc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254615"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Hantera användning och kostnader för Application Insights

> [!NOTE]
> Den här artikeln beskriver hur du analysera dataanvändning Application Insights.  Finns i följande artiklar för relaterad information.
> - [Övervaka användning och uppskattade kostnader](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) beskriver hur du visar användning och beräknade kostnader för flera Azure övervakningsfunktioner för olika prissättningsmodeller. Det beskriver också hur du ändrar din prissättningsmodell.

Om du har frågor om hur prissättningen fungerar för Application Insights kan du ställa en fråga i vårt [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Prismodell

Priser för [Azure Application Insights] [ start] baserat på datavolymen som matas in. Varje Application Insights-resurs debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration.

### <a name="data-volume-details"></a>Datavolymer

* Datavolym är antalet byte av telemetri som tagits emot av Application Insights. Datavolym mäts som storleken på den okomprimerade data paket för JSON som tas emot av Application Insights från ditt program. För [tabelldata som importerats till Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datavolym mäts som den okomprimerade storleken för filer som skickas till Application Insights.
* Programmets datavolymavgifter rapporteras på en ny faktureringsmätaren börjar med namnet **datainmatning** från och med April 2018. Det här ny mätare är delas mellan övervakning tekniker, till exempel program Insights och Log Analytics och är för närvarande under namnet på tjänsten **Log Analytics**. 
* [Live Metrics Stream](../../azure-monitor/app/live-stream.md) data inte räknas för prissättning.

Löpande priser i din valuta och region, se [priser för Application Insights][pricing].

### <a name="multi-step-web-tests"></a>Webbtester med flera steg

[Webbtester med flera steg](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) innebära en extra avgift. Webbtester med flera steg är webbtester som utför en sekvens med åtgärder.

Det finns ingen avgift för *pinga tester* på en enda sida. Telemetri från Pingtest och flera steg tester debiteras samma som andra telemetrin från din app.

## <a name="review-usage-and-estimate-costs"></a>Granska användning och uppskatta kostnader

Application Insights gör det lätt att förstå vad dina kostnader kan antas vara baserad på senaste användningsmönster. Kom igång i Azure-portalen för Application Insights-resurs, gå till den **användning och uppskattade kostnader** sidan:

![Välj prissättning](./media/pricing/pricing-001.png)

A. Granska din datavolym för månaden. Detta omfattar alla data som har tagit emot och bevaras (efter eventuella [sampling](../../azure-monitor/app/sampling.md)) från din server och klientappar och tillgänglighetstester.  
B. En separat avgift tas ut för [webbtester med flera steg](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests). (Detta omfattar inte enkel tillgänglighetstester som ingår i data volym kostnad.)  
C. Visa datatrender för den senaste månaden.  
D. Aktivera datainmatning [sampling](../../azure-monitor/app/sampling.md).   
E. Ange den dagliga Volymgränsen för data.  

Om du vill undersöka din Application Insights-användning molnmiljön, öppna den **mått** , lägga till mått med namnet ”punkt datavolym” och välj sedan den *gäller dela* möjlighet att dela data genom att ”telemetri objekttypen ”. 

Avgifter för Application Insights läggs till i Azure-fakturan. Du kan se information om din Azure fakturerar i den **fakturering** avsnitt av Azure portal eller i den [Azures faktureringsportal](https://account.windowsazure.com/Subscriptions). 

![I den vänstra menyn, Välj fakturering](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Datahastighet
Mängden data som du skickar är begränsad på tre sätt:

* **Sampling**: Du kan använda sampling för att minska mängden telemetri som skickas från din server och klient apps, med minimal förvrängning mått. Sampling är det primära verktyget som du kan använda för att justera mängden data som du skickar. Läs mer om [sampling funktioner](../../azure-monitor/app/sampling.md). 
* **Dagligt tak**: När du skapar en Application Insights-resurs i Azure-portalen, ange den dagliga gränsen på 100 GB/dag. När du skapar en Application Insights-resurs i Visual Studio kan standard är liten (endast 32,3 MB per dag). Dagligt tak standard anges för att underlätta testningen. Den är avsedd att användaren kommer att öka dagliga gräns innan du distribuerar appen till produktion. 

    Den maximala gränsen är 1 000 GB/dag, såvida inte du begära en högre maximal för ett program med hög trafik. 

    Var försiktig när du ställer in den dagliga gränsen. Din avsikt bör vara att *aldrig når den dagliga gränsen*. Om du når den dagliga gränsen du förlorar data under resten av dagen och du kan inte övervaka ditt program. Du kan ändra den dagliga gränsen med den **dagligt volymtak** alternativet. Du kan komma åt det här alternativet i den **användning och uppskattade kostnader** fönstret (Detta beskrivs mer utförligt senare i artikeln).
    Vi har tagit bort begränsningen för vissa prenumerationstyper som har krediter som inte kunde användas för Application Insights. Om prenumerationen har en utgiftsgräns, har tidigare dagliga taket dialogrutan anvisningar för att ta bort utgiftsgränsen och aktivera den dagliga gränsen som ska upphöjas utöver 32,3 MB per dag.
* **Begränsning av**: Nätverksbegränsningar datahastighet på 32 000 händelser per sekund, ett genomsnitt över 1 minut per instrumentationsnyckel.

*Vad händer om min app överskrider hastigheten?*

* Mängden data som appen skickar används för att utvärdera varje minut. Om det överskrider hastighet per sekund som ett genomsnitt över minut servern kan inte vissa begäranden. SDK: N buffrar data och sedan försöker skicka om den. Det sprids ut en ökning över flera minuter. Om din app skickar data konsekvent på fler än hastigheten, kommer vissa data att tas bort. (Den ASP.NET, Java och JavaScript SDK: erna försöker skicka om data på så sätt och andra SDK: er kan bara ta bort begränsas data.) Om begränsning inträffar, varnar en varning om meddelande om att detta har inträffat.

*Hur vet jag hur mycket data som min app skickar?*

Du kan använda något av följande alternativ för att se hur mycket data som appen skickar:

* Gå till den **användning och uppskattade kostnader** fönstret för att se diagrammet volym dagliga data. 
* Lägg till ett nytt diagram i Metrics Explorer. Diagram-mått, Välj **återställningspunktvolym**. Aktivera **gruppering**, och sedan grupp av **datatypen**.

## <a name="reduce-your-data-rate"></a>Minska vilken takt dina data
Här följer några saker som du kan göra för att minska din datavolym:

* Använd [Sampling](../../azure-monitor/app/sampling.md). Den här tekniken minskar vilken takt dina data utan skeva dina mått. Du förlorar inte möjligheten att navigera mellan relaterade objekt i sökningen. I server-appar fungerar sampling automatiskt.
* [Begränsa antalet Ajax-anrop som kan rapporteras](../../azure-monitor/app/javascript.md#detailed-configuration) i varje Sidvisning eller växel Ajax rapportering ska vara avstängt.
* [Redigera ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) att stänga av insamling moduler som du inte behöver. Du kan till exempel bestämma att prestandaräknare eller beroendedata är inessential.
* Dela din telemetri bland olika instrumenteringsnycklar. 
* Före aggregera mått. Om du placerar anrop till TrackMetric i din app kan du minska trafik med hjälp av överlagring som accepterar dina beräkning av medelvärdet och standardavvikelsen för en batch med mätning av faktisk användning. Du kan också använda en [före sammanställa paketet](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Hantera maximal daglig datavolym

Du kan använda den dagliga Volymgränsen för att begränsa de data som samlas in. Men om fästpunkten är uppfyllt, sker en förlust av all telemetri som skickas från ditt program för resten av dagen. Det är *inte lämpligt* ha ditt program når den dagliga gränsen. Du kan inte spåra status och prestanda för ditt program när den når den dagliga gränsen.

Istället för att använda den dagliga Volymgränsen använder [sampling](../../azure-monitor/app/sampling.md) att finjustera datavolym till önskad nivå. Använd sedan den dagliga gränsen endast som en ”sista utväg” om ditt program oväntat börjar skicka mycket högre mängder telemetri.

Ändra den dagliga gränsen i den **konfigurera** delen av Application Insights-resursen i den **användning och uppskattade kostnader** väljer **dagligt tak**.

![Justera den dagliga Volymgränsen telemetri](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Samling
[Sampling](../../azure-monitor/app/sampling.md) är en metod för att minska den hastighet med vilken telemetri skickas till din app, samtidigt som du behåller möjligheten att hitta relaterade händelser under diagnostiksökningar. Du kan också behålla rätt händelseantal.

Sampling är ett effektivt sätt att minska kostnaderna och stannar inom din månatliga kvot. Samplingsalgoritmen behåller relaterade objekt i telemetrin så, till exempel när du använder Search kan du hitta den begäran som rör ett visst undantag. Algoritmen bevarar också rätt antal så att du kan se värdena i Metric Explorer för begäranhastigheter, undantag pris och andra antal.

Det finns flera typer av sampling.

* [Adaptiv sampling](../../azure-monitor/app/sampling.md) är standard för ASP.NET-SDK. Adaptiv sampling justeras automatiskt till volymen telemetri som din app skickar. Den fungerar automatiskt i SDK i din webbapp så att telemetri nätverkstrafik minskas. 
* *Inmatningssampling* är ett alternativ som fungerar på den punkt där telemetrin från din app kommer in Application Insights-tjänsten. Inmatningssampling påverkar inte mängden telemetri som skickas från din app, men det minskar den volym som sparas av tjänsten. Du kan använda inmatningssampling för att minska den kvot som förbrukats av telemetri från webbläsare och andra SDK: er.

Ange inmatningssampling genom att gå till den **priser** fönstret:

![Välj panelen exempel i kvoter och priser fönstret, och välj sedan en bråkdel av linjer](./media/pricing/pricing-004.png)

> [!WARNING]
> Den **datasampling** fönstret styr endast värdet för inmatningssampling. Den visas inte samplingsfrekvensen som tillämpas av Application Insights SDK i din app. Om den inkommande telemetrin agentprestandavyn redan i SDK, används inte av inmatningssampling.
>

För att identifiera faktiska samplingsfrekvensen, oavsett om den har tillämpats, använda en [analysfråga](analytics.md). Frågan ser ut så här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Bevaras i varje post, `itemCount` anger hur många ursprungliga poster som representerar. Det är lika med 1 + antalet tidigare borttagna poster. 

## <a name="automation"></a>Automation

Du kan skriva ett skript för att ange prisplan med hjälp av Azure Resource Manager. [Lär dig mer](powershell.md#price).

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Inaktivera e-postmeddelanden med daglig gräns

Inaktivera de dagliga volume cap e-postmeddelandena, under den **konfigurera** delen av Application Insights-resursen i den **användning och uppskattade kostnader** väljer **dagligt tak** . Det finns inställningar för att skicka e-postmeddelande när gränsen har uppnåtts, samt när en justerbar varningsnivå har uppnåtts. Om du vill inaktivera alla dagligen relaterade cap volym e-postmeddelanden avmarkera båda rutorna.

## <a name="legacy-enterprise-pricing-plan"></a>Äldre Enterprise-prisplanen

För tidigt för Azure Application Insights finns det fortfarande två möjliga två prisavtalen: Basic och Enterprise. Grundläggande prisplanen är densamma som beskrivs ovan och är standard-prenumerationen. Den innehåller alla plan företagsfunktioner, utan extra kostnad. Basic-avtalet fakturor främst på mängden data som matas in. 

Enterprise-avtalet har en avgift per nod, och varje nod som tar emot en dagliga datakvoten. I företaget debiteras prisavtal, du för data som matas in ovan ingår tilldelningen. Om du använder Operations Management Suite, bör du välja Enterprise-avtalet. 

Löpande priser i din valuta och region, se [priser för Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> I April 2018 kommer vi [introduceras](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) en ny Prismodell för Azure-övervakning. Den här modellen antar en enkel modell för ”betala per användning” i den fullständiga portföljen av övervakningstjänster. Läs mer om den [nya prissättningsmodellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hur till [utvärdera effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) baserat på dina användningsmönster och [så här att den nya modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise-avtalet och Operations Management Suite-prenumerationens rättigheter

Kunder som köper Operations Management Suite E1 och E2 kan hämta Application Insights Enterprise som en ytterligare komponent utan ytterligare kostnad som [tidigare meddelats](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Mer specifikt ingår varje enhet i Operations Management Suite E1 eller E2 en nod för Application Insights Enterprise-planen. Varje nod för Application Insights ingår upp till 200 MB data per dag (separat från Log Analytics-datainmatning), med en datakvarhållning på 90 dagar utan extra kostnad. Planen beskrivs mer i detalj senare i artikeln. 

Eftersom den här planen är gäller endast för kunder med en Operations Management Suite-prenumeration kan ser kunder som inte har en Operations Management Suite-prenumeration inte något alternativ att välja den här planen.

> [!NOTE]
> För att säkerställa att du får den här rättigheten, måste Application Insights-resurser vara i företaget prisavtal. Den här rättigheten gäller endast som noder. Application Insights-resurser i Basic-avtalet inte är medveten om någon förmån. Den här rättigheten inte visas i de uppskattade kostnaderna som visas i den **användning och uppskattade kostnader** fönstret. Om du flyttar en prenumeration till den nya Azure övervakning prismodellen i April 2018 är också den enda planen som är tillgänglig i Basic-avtal. Flytta en prenumeration till den nya Azure-övervakning prismodellen är inte lämpligt om du har en Operations Management Suite-prenumeration.

### <a name="how-the-enterprise-plan-works"></a>Så här fungerar företagsplanen

* Du betalar för varje nod som skickar telemetri för alla appar i Enterprise-avtalet.
  * En *noden* är en fysisk eller virtuell server-dator eller en platform-as-a-service-rollinstans som är värd för din app.
  * Utveckling av datorer, klientwebbläsare och mobila enheter räknas inte som noder.
  * Om din app har flera komponenter som skickar telemetri, till exempel en webbtjänst och en serverarbetsroll räknas komponenterna separat.
  * [Live Metrics Stream](../../azure-monitor/app/live-stream.md) data inte räknas för prissättning. I en prenumeration är en av dina avgifter per nod, inte per app. Om du har fem noder som skickar telemetri för 12 appar, avgiften gäller för fem noder.
* Även om avgifter anges per månad, debiteras du bara för en timme som en nod skickar telemetri från en app. Per timme avgiften gäller den citerade månadskostnaden divideras med 744 (antal timmar under en månad som 31 dagar).
* En data-volymfördelning på 200 MB per dag får för varje nod som har identifierats (med per timme kornighet). Oanvända dataallokeringen är inte överföras mellan en dag till nästa.
  * Om du väljer Enterprise prisavtal får varje prenumeration en daglig datakvot baserat på antalet noder som skickar telemetri till Application Insights-resurser i prenumerationen. Så om du har fem noder som skickar data hela dagen, får du en datapool på 1 GB som tillämpas på alla Application Insights-resurser i prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om en viss dag får Application Insights-resurser mer data än vad som ingår i allokeringen av dagliga data för den här prenumerationen, gäller priserna för per GB extra data. 
  * Den dagliga datakvoten beräknas som antalet timmar under dagen (med UTC) att varje nod skickar telemetri dividerat med 24 multiplicerat med 200 MB. Så om du har fyra noder som skickar telemetri under 15 i 24 timmar under dagen inkluderade data för den dagen skulle vara ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Till samma pris 2,30 USD per GB för överförbrukning är avgiften 1,15 USD om noderna som skickar 1 GB data den dagen.
  * Enterprise-plan dagliga kvoten delas inte med program som du har valt Basic-avtal. Oanvända tilldelningen är inte överföras från dag till dag. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exempel på hur du fastställer distinkta nodantal

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

## <a name="next-steps"></a>Nästa steg

* [Sampling](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/