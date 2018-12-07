---
title: Hantera priser och datavolymer för Azure Application Insights | Microsoft Docs
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
ms.date: 08/11/2018
ms.author: mbullwin
ms.openlocfilehash: a81cb9041b905cfb00183981036116fbc61f376a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000881"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Hantera priser och datavolymer i Application Insights

> [!NOTE]
> Den här artikeln beskriver hur du analysera dataanvändning Application Insights.  Finns i följande artiklar för relaterad information.
> - [Övervaka användning och uppskattade kostnader](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) beskriver hur du visar användning och beräknade kostnader för flera Azure övervakningsfunktioner för olika prissättningsmodeller. Det beskriver också hur du ändrar din prissättningsmodell.

Priser för [Azure Application Insights] [ start] baserat på datavolymen per program. Varje Application Insights-resurs debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration.

Application Insights har två prisavtalen: Basic och Enterprise. Grundläggande prisplanen är standardplanen. Den innehåller alla plan företagsfunktioner, utan extra kostnad. Basic-avtalet fakturor främst på mängden data som matas in. 

Enterprise-avtalet har en avgift per nod, och varje nod som tar emot en dagliga datakvoten. I företaget debiteras prisavtal, du för data som matas in ovan ingår tilldelningen. Om du använder Operations Management Suite, bör du välja Enterprise-avtalet. 

Om du har frågor om hur prissättningen fungerar för Application Insights kan du ställa en fråga i vårt [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Prisinformation

Löpande priser i din valuta och region, se [priser för Application Insights][pricing].

> [!NOTE]
> I April 2018 kommer vi [introduceras](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) en ny Prismodell för Azure-övervakning. Den här modellen antar en enkel modell för ”betala per användning” i den fullständiga portföljen av övervakningstjänster. Läs mer om den [nya prissättningsmodellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hur till [utvärdera effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) baserat på dina användningsmönster och [så här att den nya modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Basic-avtal

Basic-avtalet är standardinställningen prisavtal när en ny Application Insights-resurs skapas. Basic-avtalet är optimal för alla kunder utom de som har en Operations Management Suite-prenumeration.

* På Basic-avtal debiteras du per datavolym. Datavolym är antalet byte av telemetri som tagits emot av Application Insights. Datavolym mäts som storleken på den okomprimerade data paket för JSON som tas emot av Application Insights från ditt program. För [tabelldata som importerats till Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datavolym mäts som den okomprimerade storleken för filer som skickas till Application Insights.
* Programmets datavolymavgifter rapporteras på en ny faktureringsmätaren börjar med namnet **datainmatning** från och med April 2018. Detta ny mätare är delas mellan övervakning tekniker, till exempel program Insights och Log Analytics och är för närvarande under namnet på tjänsten **Apptjänster** (och ändra snart till **Log Analytics**). 
* [Live Metrics Stream](app-insights-live-stream.md) data inte räknas för prissättning.
* [Löpande export](app-insights-export-telemetry.md) och [Azure Log Analytics-anslutningsappen](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) är tillgängliga utan extra kostnad i Basic-avtalet från och med April 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise-avtalet och Operations Management Suite-prenumerationens rättigheter

Kunder som köper Operations Management Suite E1 och E2 kan hämta Application Insights Enterprise som en ytterligare komponent utan ytterligare kostnad som [tidigare meddelats](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Mer specifikt ingår varje enhet i Operations Management Suite E1 eller E2 en nod för Application Insights Enterprise-planen. Varje nod för Application Insights ingår upp till 200 MB data per dag (separat från Log Analytics-datainmatning), med en datakvarhållning på 90 dagar utan extra kostnad. Planen beskrivs mer i detalj senare i artikeln. 

Eftersom den här planen är gäller endast för kunder med en Operations Management Suite-prenumeration kan ser kunder som inte har en Operations Management Suite-prenumeration inte något alternativ att välja den här planen.

> [!NOTE]
> För att säkerställa att du får den här rättigheten, måste Application Insights-resurser vara i företaget prisavtal. Den här rättigheten gäller endast som noder. Application Insights-resurser i Basic-avtalet inte är medveten om någon förmån. Den här rättigheten inte visas i de uppskattade kostnaderna som visas i den **användning och uppskattade kostnader** fönstret. Om du flyttar en prenumeration till den nya Azure övervakning prismodellen i April 2018 är också den enda planen som är tillgänglig i Basic-avtal. Flytta en prenumeration till den nya Azure-övervakning prismodellen är inte lämpligt om du har en Operations Management Suite-prenumeration.

Läs mer om Enterprise-avtalet, [Enterprise prisinformation](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Webbtester med flera steg

[Webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests) innebära en extra avgift. Webbtester med flera steg är webbtester som utför en sekvens med åtgärder.

Det finns ingen avgift för *pinga tester* på en enda sida. Telemetri från Pingtest och flera steg tester debiteras samma som andra telemetrin från din app.

## <a name="review-pricing-plans-and-estimate-costs"></a>Granska prisinformation och uppskatta kostnader

Application Insights gör det lätt att förstå prissättningen abonnemang som är tillgängliga och vilka kostnaderna förmodligen kommer att baseras på de senaste användningsmönster. Kom igång i Azure-portalen för Application Insights-resurs, gå till den **användning och uppskattade kostnader** fönstret:

![Välj prissättning](./media/app-insights-pricing/pricing-001.png)

A. Granska din datavolym för månaden. Detta omfattar alla data som har tagit emot och bevaras (efter eventuella [sampling](app-insights-sampling.md)) från din server och klientappar och tillgänglighetstester.  
B. En separat avgift tas ut för [webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Detta omfattar inte enkel tillgänglighetstester som ingår i data volym kostnad.)  
C. Visa datatrender för den senaste månaden.  
D. Aktivera datainmatning [sampling](app-insights-sampling.md).   
E. Ange den dagliga Volymgränsen för data.  

Avgifter för Application Insights läggs till i Azure-fakturan. Du kan se information om din Azure fakturerar i den **fakturering** avsnitt av Azure portal eller i den [Azures faktureringsportal](https://account.windowsazure.com/Subscriptions). 

![I den vänstra menyn, Välj fakturering](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Datahastighet
Mängden data som du skickar är begränsad på tre sätt:

* **Sampling**: du kan använda sampling för att minska mängden telemetri som skickas från dina server- och appar, med minimal förvrängning mått. Sampling är det primära verktyget som du kan använda för att justera mängden data som du skickar. Läs mer om [sampling funktioner](app-insights-sampling.md). 
* **Dagligt tak**: när du skapar en Application Insights-resurs i Azure portal kan den dagliga gränsen är inställd på 100 GB/dag. När du skapar en Application Insights-resurs i Visual Studio kan standard är liten (endast 32,3 MB per dag). Dagligt tak standard anges för att underlätta testningen. Den är avsedd att användaren kommer att öka dagliga gräns innan du distribuerar appen till produktion. 

    Den maximala gränsen är 1 000 GB/dag, såvida inte du begära en högre maximal för ett program med hög trafik. 

    Var försiktig när du ställer in den dagliga gränsen. Din avsikt bör vara att *aldrig når den dagliga gränsen*. Om du når den dagliga gränsen du förlorar data under resten av dagen och du kan inte övervaka ditt program. Du kan ändra den dagliga gränsen med den **dagligt volymtak** alternativet. Du kan komma åt det här alternativet i den **användning och uppskattade kostnader** fönstret (Detta beskrivs mer utförligt senare i artikeln).
    Vi har tagit bort begränsningen för vissa prenumerationstyper som har krediter som inte kunde användas för Application Insights. Om prenumerationen har en utgiftsgräns, har tidigare dagliga taket dialogrutan anvisningar för att ta bort utgiftsgränsen och aktivera den dagliga gränsen som ska upphöjas utöver 32,3 MB per dag.
* **Begränsning**: nätverksbegränsningar datahastighet till 32 000 händelser per sekund i genomsnitt över 1 minut per instrumentationsnyckel.

*Vad händer om min app överskrider hastigheten?*

* Mängden data som appen skickar används för att utvärdera varje minut. Om det överskrider hastighet per sekund som ett genomsnitt över minut servern kan inte vissa begäranden. SDK: N buffrar data och sedan försöker skicka om den. Det sprids ut en ökning över flera minuter. Om din app skickar data konsekvent på fler än hastigheten, kommer vissa data att tas bort. (Den ASP.NET, Java och JavaScript SDK: erna försöker skicka om data på så sätt och andra SDK: er kan bara ta bort begränsas data.) Om begränsning inträffar, varnar en varning om meddelande om att detta har inträffat.

*Hur vet jag hur mycket data som min app skickar?*

Du kan använda något av följande alternativ för att se hur mycket data som appen skickar:

* Gå till den **användning och uppskattade kostnader** fönstret för att se diagrammet volym dagliga data. 
* Lägg till ett nytt diagram i Metrics Explorer. Diagram-mått, Välj **återställningspunktvolym**. Aktivera **gruppering**, och sedan grupp av **datatypen**.

## <a name="reduce-your-data-rate"></a>Minska vilken takt dina data
Här följer några saker som du kan göra för att minska din datavolym:

* Använd [Sampling](app-insights-sampling.md). Den här tekniken minskar vilken takt dina data utan skeva dina mått. Du förlorar inte möjligheten att navigera mellan relaterade objekt i sökningen. I server-appar fungerar sampling automatiskt.
* [Begränsa antalet Ajax-anrop som kan rapporteras](app-insights-javascript.md#detailed-configuration) i varje Sidvisning eller växel Ajax rapportering ska vara avstängt.
* [Redigera ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) att stänga av insamling moduler som du inte behöver. Du kan till exempel bestämma att prestandaräknare eller beroendedata är inessential.
* Dela din telemetri bland olika instrumenteringsnycklar. 
* Före aggregera mått. Om du placerar anrop till TrackMetric i din app kan du minska trafik med hjälp av överlagring som accepterar dina beräkning av medelvärdet och standardavvikelsen för en batch med mätning av faktisk användning. Du kan också använda en [före sammanställa paketet](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Hantera maximal daglig datavolym

Du kan använda den dagliga Volymgränsen för att begränsa de data som samlas in. Men om fästpunkten är uppfyllt, sker en förlust av all telemetri som skickas från ditt program för resten av dagen. Det är *inte lämpligt* ha ditt program når den dagliga gränsen. Du kan inte spåra status och prestanda för ditt program när den når den dagliga gränsen.

Istället för att använda den dagliga Volymgränsen använder [sampling](app-insights-sampling.md) att finjustera datavolym till önskad nivå. Använd sedan den dagliga gränsen endast som en ”sista utväg” om ditt program oväntat börjar skicka mycket högre mängder telemetri.

Ändra den dagliga gränsen i den **konfigurera** delen av Application Insights-resursen i den **användning och uppskattade kostnader** väljer **dagligt tak**.

![Justera den dagliga Volymgränsen telemetri](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Samling
[Sampling](app-insights-sampling.md) är en metod för att minska den hastighet med vilken telemetri skickas till din app, samtidigt som du behåller möjligheten att hitta relaterade händelser under diagnostiksökningar. Du kan också behålla rätt händelseantal.

Sampling är ett effektivt sätt att minska kostnaderna och stannar inom din månatliga kvot. Samplingsalgoritmen behåller relaterade objekt i telemetrin så, till exempel när du använder Search kan du hitta den begäran som rör ett visst undantag. Algoritmen bevarar också rätt antal så att du kan se värdena i Metric Explorer för begäranhastigheter, undantag pris och andra antal.

Det finns flera typer av sampling.

* [Adaptiv sampling](app-insights-sampling.md) är standard för ASP.NET-SDK. Adaptiv sampling justeras automatiskt till volymen telemetri som din app skickar. Den fungerar automatiskt i SDK i din webbapp så att telemetri nätverkstrafik minskas. 
* *Inmatningssampling* är ett alternativ som fungerar på den punkt där telemetrin från din app kommer in Application Insights-tjänsten. Inmatningssampling påverkar inte mängden telemetri som skickas från din app, men det minskar den volym som sparas av tjänsten. Du kan använda inmatningssampling för att minska den kvot som förbrukats av telemetri från webbläsare och andra SDK: er.

Ange inmatningssampling genom att gå till den **priser** fönstret:

![Välj panelen exempel i kvoter och priser fönstret, och välj sedan en bråkdel av linjer](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Den **datasampling** fönstret styr endast värdet för inmatningssampling. Den visas inte samplingsfrekvensen som tillämpas av Application Insights SDK i din app. Om den inkommande telemetrin agentprestandavyn redan i SDK, används inte av inmatningssampling.
>

För att identifiera faktiska samplingsfrekvensen, oavsett om den har tillämpats, använda en [analysfråga](app-insights-analytics.md). Frågan ser ut så här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Bevaras i varje post, `itemCount` anger hur många ursprungliga poster som representerar. Det är lika med 1 + antalet tidigare borttagna poster. 

## <a name="automation"></a>Automation

Du kan skriva ett skript för att ange prisplan med hjälp av Azure Resource Manager. [Lär dig mer](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Inaktivera e-postmeddelanden med daglig gräns

Inaktivera de dagliga volume cap e-postmeddelandena, under den **konfigurera** delen av Application Insights-resursen i den **användning och uppskattade kostnader** väljer **dagligt tak** . Det finns inställningar för att skicka e-postmeddelande när gränsen har uppnåtts, samt när en justerbar varningsnivå har uppnåtts. Om du vill inaktivera alla dagligen relaterade cap volym e-postmeddelanden avmarkera båda rutorna.

## <a name="next-steps"></a>Nästa steg

* [Sampling](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/