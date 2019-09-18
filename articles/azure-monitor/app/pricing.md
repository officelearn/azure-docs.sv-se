---
title: Hantera användning och kostnader för Azure Application Insights | Microsoft Docs
description: Hantera telemetri volymer och övervaka kostnader i Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 09/17/2019
ms.author: dalek
ms.openlocfilehash: 62f2ea36468e30b20ef08bde21bfde961faae8f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067016"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Hantera användning och kostnader för Application Insights

> [!NOTE]
> Den här artikeln beskriver hur du analyserar data användnings Application Insights.  Finns i följande artiklar för relaterad information.
> - [Övervaka användning och uppskattade kostnader](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) beskriver hur du visar användning och beräknade kostnader för flera Azure övervakningsfunktioner för olika prissättningsmodeller. Det beskriver också hur du ändrar din prissättningsmodell.

Om du har frågor om hur prissättningen fungerar för Application Insights kan du publicera en fråga i vårt [Forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Prismodell

Prissättningen för [Azure Application insikter][start] baseras på data volymen. Varje Application Insights resurs debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration.

### <a name="data-volume-details"></a>Information om data volym

* Data volym är antalet byte med telemetri som mottagits av Application Insights. Data volymen mäts som storleken på det okomprimerade JSON-datapaketet som tas emot av Application Insights från ditt program. För [tabell data som importeras till Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)mäts data volymen som okomprimerad storlek på filer som skickas till Application Insights.
* Dina programs data volym kostnader rapporteras nu vid en ny fakturerings mätare med namnet **data inmatning** från och med april 2018. Den nya mätaren delas mellan övervaknings tekniker, till exempel program insikter och Log Analytics och är för närvarande under tjänst namnet **Log Analytics**. 
* [Live Metrics Stream](../../azure-monitor/app/live-stream.md) data räknas inte i prissättnings syfte.

> [!NOTE]
> Alla priser som visas i skärm bilder i den här artikeln är till exempel endast avsedda. För aktuella priser i din valuta och region, se [Application Insights priser][pricing].

### <a name="multi-step-web-tests"></a>Webbtester med flera steg

[Webbtester med flera steg](../../azure-monitor/app/availability-multistep.md) debiteras ytterligare en kostnad. Webbtester med flera steg är webbtester som utför en sekvens med åtgärder.

Det finns ingen separat avgift för *ping-test* av en enda sida. Telemetri från ping-test och multi-Step-tester debiteras på samma sätt som andra telemetri från din app.

## <a name="review-usage-and-estimate-costs"></a>Granska användning och beräkna kostnader

Application Insights är det enkelt att förstå vad dina kostnader sannolikt kommer att baseras på de senaste användnings mönstren. Kom igång genom att gå till sidan **användning och uppskattade kostnader** i Azure Portal för resursen Application Insights:

![Välj prissättning](./media/pricing/pricing-001.png)

A. Granska din data volym för månaden. Detta omfattar alla data som tas emot och bevaras (efter alla [samplingar](../../azure-monitor/app/sampling.md)) från Server-och klient program och från tillgänglighets test.  
B. En separat avgift görs för [webbtester med flera steg](../../azure-monitor/app/availability-multistep.md). (Detta omfattar inte test av enkel tillgänglighet, som ingår i data volym avgiften.)  
C. Visa data volym trender för den senaste månaden.  
D. Aktivera [sampling](../../azure-monitor/app/sampling.md)av data inmatning.   
E. Ange daglig data volyms kap.  

Om du vill undersöka Application Insightss användningen mer djup, öppnar du sidan **mått** , lägger till måttet "data punkts volym" och väljer sedan alternativet *Använd delning* för att dela data efter "telemetri objekt typ". 

Application Insights avgifter läggs till på din Azure-faktura. Du kan se information om din Azure-faktura i **fakturerings** avsnittet i Azure Portal eller på [Azures fakturerings Portal](https://account.windowsazure.com/Subscriptions). 

![På den vänstra menyn väljer du fakturering](./media/pricing/02-billing.png)

## <a name="managing-your-data-volume"></a>Hantera din data volym 

För att förstå hur mycket data din app skickar kan du:

* Gå till fönstret **användning och uppskattad kostnad** för att se diagrammet över dagliga data volymer. 
* I Metrics Explorer lägger du till ett nytt diagram. För diagram måttet väljer du **data punkts volym**. Aktivera **gruppering**och gruppera sedan efter **datatyp**.
* `systemEvents` Använd data typen. Om du till exempel vill se data volymen som matats in under den senaste dagen skulle frågan bli:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Den här frågan kan användas i en [Azure logg avisering](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) för att konfigurera aviseringar på data volymer. 

Mängden data som du skickar kan hanteras på tre sätt:

* **Sampling**: Du kan använda sampling för att minska mängden telemetri som skickas från servern och klientens appar, med minimal snedvridning av mått. Sampling är det primära verktyget som du kan använda för att justera mängden data som du skickar. Lär dig mer om [samplings funktioner](../../azure-monitor/app/sampling.md).
 
* **Dagligt tak**: När du skapar en Application Insights-resurs i Azure Portal är den dagliga gränsen inställd på 100 GB/dag. När du skapar en Application Insights resurs i Visual Studio är standardvärdet liten (endast 32,3 MB/dag). Standardvärdet för dagligt tak är inställt för att under lätta testning. Det är avsett att användaren får den dagliga gränsen innan du distribuerar appen till produktion. 

    Den största gränsen är 1 000 GB/dag om du inte begär en högre gräns för ett program med hög trafik. 
    
    Varnings meddelanden om den dagliga gränsen skickas till konton som är medlemmar i dessa roller för din Application Insights-resurs: "ServiceAdmin", "AccountAdmin", "medadministratör", "ägare".

    Använd försiktighet när du anger den dagliga gränsen. Avsikten bör vara att *aldrig träffa den dagliga*gränsen. Om du når den dagliga gränsen förlorar du data för resten av dagen och du kan inte övervaka ditt program. Om du vill ändra den dagliga begränsningen använder du alternativet för **daglig volym begränsning** . Du kan komma åt det här alternativet i fönstret **användning och uppskattade kostnader** (detta beskrivs mer detaljerat längre fram i artikeln).
    
    Vi har tagit bort begränsningen för vissa prenumerations typer som har kredit som inte kan användas för Application Insights. Om prenumerationen har en utgifts gräns har den dagliga Cap-dialog rutan instruktioner för att ta bort utgifts gränsen och göra det möjligt att öka den dagliga gränsen för mer än 32,3 MB/dag.
    
* **Begränsning**: Begränsning begränsar data hastigheten till 32 000 händelser per sekund, i genomsnitt över 1 minut per Instrumentation-nyckel. Den data mängd som din app skickar utvärderas varje minut. Om det överstiger den per sekund som genomsnitts priset under minuten, vägrar-servern vissa begär Anden. SDK buffrar data och försöker sedan skicka det igen. Den sprider en överspänning på flera minuter. Om din app kontinuerligt skickar data med mer än begränsnings frekvensen kommer vissa data att tas bort. (ASP.NET-, Java-och JavaScript-SDK: er försöker skicka data på det här sättet igen. andra SDK: er kanske bara släpper begränsade data.) Om det uppstår en begränsning visas en varning om att ett meddelande har inträffat.

## <a name="reduce-your-data-volume"></a>Minska din data volym

Här följer några saker som du kan göra för att minska din data volym:

* Använd [sampling](../../azure-monitor/app/sampling.md). Den här tekniken minskar data hastigheten utan att skeva måtten. Du förlorar inte möjligheten att navigera mellan relaterade objekt i sökningen. I Server appar fungerar samplingen automatiskt.
* [Begränsa antalet AJAX-anrop som kan rapporteras](../../azure-monitor/app/javascript.md#configuration) i varje sid visning eller inaktivera Ajax-rapportering.
* [Redigera ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) för att inaktivera de insamlings moduler som du inte behöver. Du kan till exempel bestämma att prestanda räknare eller beroende data inte är nödvändiga.
* Dela din telemetri mellan separata instrument nycklar. 
* Föraggregerade mått. Om du skickar anrop till TrackMetric i din app kan du minska trafiken genom att använda överlagringen som godkänner beräkningen av medelvärdet och standard avvikelsen för en batch med mått. Eller så kan du använda ett [paket för](https://www.myget.org/gallery/applicationinsights-sdk-labs)församlings sammansättning.

## <a name="manage-the-maximum-daily-data-volume"></a>Hantera maximal daglig datavolym

Du kan använda den dagliga volym begränsningen för att begränsa de data som samlas in. Men om höljet uppfylls uppstår en förlust av all telemetri som skickas från ditt program för resten av dagen. Vi *rekommenderar inte* att programmet träffar det dagliga höljet. Du kan inte spåra programmets hälsa och prestanda när den når den dagliga gränsen.

I stället för att använda den dagliga volym gränsen använder du [sampling](../../azure-monitor/app/sampling.md) för att justera data volymen till önskad nivå. Använd sedan den dagliga begränsningen endast som "sista utväg" om ditt program inte börjar att skicka mycket större telemetri.

Om du vill ändra den dagliga belastningen går du till avsnittet **Konfigurera** i Application Insights resurs, på sidan **användning och uppskattade kostnader** och väljer **dagligt tak**.

![Justera volym begränsningen för daglig telemetri](./media/pricing/pricing-003.png)

Om du vill [ändra den dagliga gränsen via Azure Resource Manager](../../azure-monitor/app/powershell.md)är `dailyQuota`egenskapen som ska ändras.  Via Azure Resource Manager kan du också ställa in `dailyQuotaResetTime` och använda det dagliga `warningThreshold`taket. 

## <a name="sampling"></a>Samling
[Sampling](../../azure-monitor/app/sampling.md) är en metod för att minska den hastighet som telemetri skickas till din app, samtidigt som du behåller möjligheten att hitta relaterade händelser under diagnostiska sökningar. Du behåller också korrekta antal händelser.

Sampling är ett effektivt sätt att minska kostnaderna och hålla dig inom din månads kvot. Samplings algoritmen behåller relaterade objekt för telemetri, så om du till exempel använder Sök kan du hitta begäran som är relaterad till ett visst undantag. Algoritmen behåller också de korrekta värdena så att du ser rätt värden i Metric Explorer för begär ande frekvens, undantags frekvens och andra antal.

Det finns flera former av sampling.

* [Adaptiv sampling](../../azure-monitor/app/sampling.md) är standard för ASP.NET SDK. Anpassningsbara samplingar justerar automatiskt till den telemetri som appen skickar. Den fungerar automatiskt i SDK: n i din webbapp så att telemetri i nätverket minskas. 
* Inmatnings *sampling* är ett alternativ som fungerar på den plats där telemetri från din app går in i Application Insights tjänsten. Provtagnings sampling påverkar inte mängden telemetri som skickas från din app, men den minskar volymen som behålls av tjänsten. Du kan använda samplings sampling för att minska den kvot som används av telemetri från webbläsare och andra SDK: er.

Om du vill ange insamlings sampling går du till fönstret **priser** :

![I fönstret kvot och priser väljer du panelen exempel och väljer sedan en samplings fraktion](./media/pricing/pricing-004.png)

> [!WARNING]
> **Data samplings** fönstret styr bara värdet för inmatnings sampling. Den visar inte samplings frekvensen som används av Application Insights SDK i din app. Om den inkommande Telemetrin redan har samplats i SDK: n, används inte insamlings sampling.
>

Använd en [analys fråga](analytics.md)för att identifiera den faktiska samplings frekvensen, oavsett var den tillämpades. Frågan ser ut så här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

I varje kvarhållen post `itemCount` anger antalet ursprungliga poster som den representerar. Det är lika med 1 + antalet tidigare borttagna poster. 

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

> [!NOTE]
> Vi har tillfälligt tagit bort den här funktionen medan vi åtgärdar ett möjligt problem.  Vi kommer att få tillbaka den första veckan i oktober 2019.

Standard kvarhållning av Application Insights resurser är 90 dagar. Olika bevarande perioder kan väljas för varje Application Insights resurs. Den fullständiga uppsättningen tillgängliga kvarhållningsperiod är 30, 60, 90, 120, 180, 270, 365, 550 eller 730 dagar. 

Om du vill ändra kvarhållning går du till sidan **användning och uppskattade kostnader** från Application Insights resursen och väljer alternativet **data kvarhållning** :

![Justera volym begränsningen för daglig telemetri](./media/pricing/pricing-005.png)

När faktureringen är aktive rad för längre kvarhållning debiteras data som är längre än 90 dagar som samma pris som för närvarande faktureras för Azure Log Analytics data kvarhållning. Läs mer på [sidan Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/). Håll dig uppdaterad om varierande behållnings förlopp genom [röstning för det här förslaget](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Inaktivera dagliga Cap-e-postmeddelanden

Om du vill inaktivera de dagliga e-postmeddelandena för volym tak går du till avsnittet **Konfigurera** i Application Insights-resursen och väljer **dagligt tak**i fönstret **användning och uppskattade kostnader** . Det finns inställningar för att skicka e-post när taket har nåtts, samt när en justerbar varnings nivå har uppnåtts. Avmarkera båda rutorna om du vill inaktivera alla dagliga e-postrelaterade volym meddelanden.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Pris nivå för Legacy Enterprise (per nod)

För tidiga antaganden av Azure Application insikter finns det fortfarande två möjliga pris nivåer: Basic och Enterprise. Den grundläggande pris nivån är densamma som beskrivs ovan och är standard nivån. Den innehåller alla funktioner på företags nivå utan extra kostnad. Basic-nivån faktureras främst på mängden data som matas in. 

> [!NOTE]
> Dessa äldre pris nivåer har bytt namn. Pris nivån för företaget kallas nu **per nod** och den grundläggande pris nivån kallas nu **per GB**. Dessa nya namn används nedan och i Azure Portal.  

Nivån per nod (tidigare företag) har en avgift per nod och varje nod får en daglig data tilldelning. I pris nivån per nod debiteras du för inmatade data utöver den mängd som ingår. Om du använder Operations Management Suite bör du välja nivån per nod. 

För aktuella priser i din valuta och region, se [Application Insights priser](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> I april 2018 [introducerade](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) vi en ny pris sättnings modell för Azure-övervakning. Den här modellen antar en enkel "betala per användning"-modell i hela portföljen av övervaknings tjänster. Lär dig mer om den [nya pris sättnings modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hur du kan [bedöma effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) baserat på dina användnings mönster och [hur du kan välja den nya modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Per nod-och Operations Management Suite-prenumerations rättigheter

Kunder som köper Operations Management Suite E1 och E2 kan få Application Insights per nod som ytterligare en komponent utan extra kostnad som [tidigare](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)meddelats. Mer specifikt innehåller varje enhet av Operations Management Suite E1 och E2 en rättighet till en nod i Application Insights per nod-nivå. Varje Application Insights nod innehåller upp till 200 MB data som matas in per dag (separat från Log Analytics data inmatning), med 90 dagars data kvarhållning utan extra kostnad. Nivån beskrivs mer detaljerat längre fram i artikeln. 

Eftersom den här nivån bara gäller för kunder med en Operations Management Suite-prenumeration, ser kunder som inte har någon Operations Management Suite-prenumeration inget alternativ för att välja den här nivån.

> [!NOTE]
> För att säkerställa att du får den här behörigheten måste Application Insights-resurserna finnas på pris nivån per nod. Den här rättigheten gäller endast för noder. Application Insights resurser på nivån per GB inte inser någon förmån. Den här rättigheten visas inte i de beräknade kostnader som visas i fönstret **användning och uppskattad kostnad** . Om du flyttar en prenumeration till den nya pris sättnings modellen för Azure-övervakning i april 2018 är nivån per GB den enda tillgängliga nivån. Att flytta en prenumeration till den nya pris sättnings modellen för Azure-övervakning är inte lämpligt om du har en Operations Management Suite-prenumeration.

### <a name="how-the-per-node-tier-works"></a>Så här fungerar nivån per nod

* Du betalar för varje nod som skickar telemetri för alla appar i per nod-nivå.
  * En *nod* är en fysisk eller virtuell serverdator eller en instans av en tjänst roll som är värd för din app.
  * Utvecklings datorer, klient webbläsare och mobila enheter räknas inte som noder.
  * Om din app har flera komponenter som skickar telemetri, till exempel en webb tjänst och en backend-anställd, räknas komponenterna separat.
  * [Live Metrics Stream](../../azure-monitor/app/live-stream.md) data räknas inte i prissättnings syfte. I en prenumeration är dina avgifter per nod, inte per app. Om du har fem noder som skickar telemetri för 12 appar, är avgiften för fem noder.
* Även om avgifter offereras per månad debiteras du bara för en timme där en nod skickar telemetri från en app. Tim avgiften är den noterade månads kostnaden dividerat med 744 (antalet timmar under en månad på 31 dagar).
* En data volym tilldelning på 200 MB per dag anges för varje nod som identifieras (med en timkostnad). Oanvänd data tilldelning överförs inte från en dag till nästa.
  * Om du väljer pris nivån per nod får varje prenumeration en daglig mängd data baserat på antalet noder som skickar telemetri till Application Insights resurser i den prenumerationen. Så om du har fem noder som skickar data hela dagen har du en pool med 1 GB som tillämpas på alla Application Insights resurser i den prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om den Application Insights resurserna tar emot mer data än vad som ingår i den dagliga data tilldelningen för den här prenumerationen, gäller data avgifter per GB överanvändning. 
  * Den dagliga data tilldelningen beräknas som antalet timmar under dagen (med UTC) som varje nod skickar telemetri dividerat med 24 multiplicerat med 200 MB. Så om du har fyra noder som skickar telemetri under 15 av dygnets 24 timmar blir de inkluderade data för den dagen ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Till priset 2,30 USD per GB för data överanvändning skulle avgiften bli 1,15 USD om noderna skickar 1 GB data som dag.
  * Dag traktamentet per nod delas inte med program som du har valt nivån per GB för. Outnyttjad justering överförs inte från dag till dag. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exempel på hur du bestämmer antalet distinkta noder

| Scenario                               | Totalt antal dagliga noder |
|:---------------------------------------|:----------------:|
| 1 program med 3 Azure App Service instanser och 1 virtuell server | 4 |
| 3 program som körs på två virtuella datorer; Application Insights resurser för dessa program finns i samma prenumeration och i per nod-nivå | 2 | 
| 4 program vars program Insights-resurser finns i samma prenumeration. varje program som kör två instanser under 16 timmar med låg belastning och fyra instanser under 8 högsta timmar | 13.33 | 
| Moln tjänster med 1-arbets roll och 1 webbroll, varje som kör 2 instanser | 4 | 
| Ett Azure Service Fabric-kluster med 5 noder som kör 50 mikrotjänster; varje mikrotjänst som kör 3 instanser | 5|

* Den exakta beräkningen av noder beror på vilken Application Insights SDK ditt program använder. 
  * I SDK-versionerna 2,2 och senare rapporterar både Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) och [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) rapporten varje program värd som en nod. Exempel är dator namnet för fysiska servrar och virtuella dator värdar eller instans namnet för Cloud Services.  Det enda undantaget är ett program som bara använder [.net Core](https://dotnet.github.io/) och Application Insights Core SDK. I så fall rapporteras bara en nod för alla värdar eftersom värd namnet inte är tillgängligt. 
  * För tidigare versioner av SDK fungerar [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) som nyare SDK-versioner, men [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporterar bara en nod, oavsett antalet program värdar. 
  * Om programmet använder SDK: n för att ange **roleInstance** till ett anpassat värde, används som standard samma värde för att fastställa antalet noder. 
  * Om du använder en ny SDK-version med en app som körs från klient datorer eller mobila enheter kan antalet noder returnera ett tal som är mycket stort (på grund av det stora antalet klient datorer eller mobila enheter). 

## <a name="automation"></a>Automation

Du kan skriva ett skript för att ställa in pris nivån med hjälp av Azure Resource Management. [Lär dig mer](powershell.md#price).


## <a name="next-steps"></a>Nästa steg

* [Sampling](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/