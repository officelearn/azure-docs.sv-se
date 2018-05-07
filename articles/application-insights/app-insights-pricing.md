---
title: Hantera volymen priser och data för Azure Application Insights | Microsoft Docs
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
ms.openlocfilehash: 22c8616c1585e3f728a03a794c527cb34fc0c4eb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Hantera priser och data volym i Application Insights

Priser för [Azure Application Insights] [ start] baseras på datavolym per program. Varje Application Insights-resurs debiteras som en separat tjänst och bidrar till faktura för din Azure-prenumeration.

Application Insights har två prisnivå planer: Basic och Enterprise. Grundläggande prisavtal är standardplanen. Den innehåller alla plan företagsfunktioner, utan extra kostnad. De grundläggande planen växlar huvudsakligen på mängden data som inhämtas är. 

Enterprise-planen har en avgift per nod och varje nod som tar emot en dagliga data ersättning. I företaget debiteras priser plan, du för data som inhämtas ovan ingår ersättningen. Om du använder Operations Management Suite, bör du välja Enterprise planen. 

Om du har frågor om hur priser fungerar för Application Insights kan du ställa en fråga i vår [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Prissättning

Aktuella priser i din valuta och region, se [priser för Application Insights][pricing].

> [!NOTE]
> I April 2018 vi [introduceras](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) en ny prissättningsmodell för övervakning av Azure. Den här modellen antar en enkel ”betalning per användning” modell över komplett portfölj av övervaka tjänster. Lär dig mer om den [nya Prismodell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hur till [utvärdera effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) baserat på ditt användningsmönster och [så att använda den nya modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Grundläggande planen

Den grundläggande planen är standard priser plan när en ny Application Insights-resurs skapas. Den grundläggande planen är optimalt för alla kunder utom de som har en Operations Management Suite-prenumeration.

* I den grundläggande planen debiteras av datavolym. Datavolym är antalet byte av telemetri som tagits emot av Application Insights. Datavolymen mäts som storleken på okomprimerade JSON datapaketet som tas emot av Application Insights från ditt program. För [tabelldata importeras till Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datavolym mäts som den okomprimerade storleken för filer som skickas till Application Insights.
* Programmets data volym avgifter rapporteras på en ny fakturering mätaren med namnet **Datapåfyllning** från och med April 2018. Detta nya mätaren är delas mellan övervakning tekniker, till exempel program insikter och logganalys och är för närvarande under tjänstnamnet **Apptjänster** (och ändra snarast till **logganalys**). 
* [Live mått dataströmmen](app-insights-live-stream.md) data inte räknas för prissättning.
* [Löpande export](app-insights-export-telemetry.md) och [Azure logganalys connector](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) är tillgängliga gratis i den grundläggande planen från och med April 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Företagsplan och rättigheter för Operations Management Suite-prenumeration

Kunder som köper Operations Management Suite E1 och E2 kan hämta Application Insights Enterprise som en ytterligare komponent utan extra kostnad som [tidigare meddelats](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Varje enhet Operations Management Suite E1 och E2 innehåller främst rätt till en nod av Application Insights företagsplan. Varje nod i Application Insights innehåller upp till 200 MB data som inhämtas per dag (separat från logganalys datapåfyllning) med 90 dagars data bevaras utan extra kostnad. Planen beskrivs mer i detalj senare i artikeln. 

Eftersom den här planen är gäller endast för kunder med en Operations Management Suite-prenumeration, ser kunder som inte har en Operations Management Suite-prenumeration inte något alternativ att välja den här planen.

> [!NOTE]
> För att säkerställa att du får det här rätt måste Application Insights-resurser vara i företaget priser plan. Denna rättighet gäller endast som noder. Application Insights-resurser i den grundläggande planen upptäcker inte inga fördelar. Den här rätt inte visas i de uppskattade kostnaderna som visas i den **användnings- och uppskattade kostnaden** fönstret. Om du flyttar en prenumeration till nya Azure övervakning prismodellen i April 2018 är också bara planen som är tillgängliga i den grundläggande planen. Flytta en prenumeration till nya Azure övervakning prismodellen är lämpligt om du har en Operations Management Suite-prenumeration.

Mer information om Enterprise-planen finns [Enterprise prisinformation](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Webbtester med flera steg

[Flera steg webbtester](app-insights-monitor-web-app-availability.md#multi-step-web-tests) innebära en extra kostnad. Flera steg webbtester är webbtester som utför en sekvens med åtgärder.

Det finns ingen särskild avgift för *pinga tester* för en enstaka sida. Telemetri från Pingtest och flera steg test debiteras samma som andra telemetri från din app.

## <a name="review-pricing-plans-and-estimate-costs"></a>Granska prisnivå planer och uppskatta kostnader

Application Insights gör det enkelt att förstå de prisnivå planer som är tillgängliga och vilka dina kostnader sannolikt kommer att baseras på de senaste användningsmönster. Om du vill komma igång med Azure-portalen för Application Insights-resurs, gå till den **användning och de uppskattade kostnaderna** fönstret:

![Välj prisnivå](./media/app-insights-pricing/pricing-001.png)

A. Granska dina datavolym för månaden. Detta omfattar alla data som har tagit emot och behålls (efter något [provtagning](app-insights-sampling.md)) från din server och -klientprogram och tillgänglighetstester.  
B. En separat kostnad görs efter [webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Detta innehåller enkla tillgänglighetstester som ingår i data volym kostnad.)  
C. Visa datatrender för den senaste månaden.  
D. Aktivera datapåfyllning [provtagning](app-insights-sampling.md).   
E. Ange dagliga data volym sockeln.  

Application Insights avgifter läggs till fakturan Azure. Du kan se information om din Azure debiterar i den **fakturering** avsnitt av Azure-portalen eller i den [fakturering Azure-portalen](https://account.windowsazure.com/Subscriptions). 

![I den vänstra menyn väljer du fakturering](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Datahastighet
Mängden data som du skickar är begränsad på tre sätt:

* **Provtagning**: du kan använda beräkningarna för att minska mängden telemetri som skickas från din server och klient appar, med minimal förvrängning av mått. Sampling är det viktigaste verktyget som du kan använda för att justera mängden data som du skickar. Lär dig mer om [provtagning funktioner](app-insights-sampling.md). 
* **Dagliga cap**: när du skapar en Application Insights-resurs i Azure portal dagliga fästpunkten är inställd på 100 GB/dag. När du skapar en Application Insights-resurs i Visual Studio, standard är liten (endast 32,3 MB/dag). Dagliga cap-standard anges för att underlätta testningen. Avsikten är att användaren höjer dagliga fästpunkten innan du distribuerar appen till produktionen. 

    Högsta kapacitet är 1 000 GB/dag om du begär en högre maximal för ett program med hög trafik. 

    Var försiktig när du ställer in dagliga sockeln. Din avsikt bör vara att *aldrig träffar dagliga fästpunkten*. Om du väljer dagliga fästpunkten förlorar du data för resten av dagen och du kan övervaka ditt program. Du kan ändra dagliga fästpunkten den **daglig volym cap** alternativet. Du kan komma åt det här alternativet i den **användning och de uppskattade kostnaderna** fönstret (det beskrivs i detalj senare i artikeln).
    Vi har tagit bort begränsningen på vissa prenumerationstyper av som har kredit som inte kunde användas för Application Insights. Om prenumerationen har en utgiftsgräns, har dagliga cap-dialogrutan tidigare instruktioner för att ta bort utgiftsgränsen och aktivera dagliga fästpunkten höjas utöver 32,3 MB/dag.
* **Begränsning av**: Throttling-begränsning datahastighet 32 000 händelser per sekund, var i genomsnitt över 1 minut.

*Vad händer om min app överskrider den begränsade hastigheten?*

* Mängden data som appen skickar utvärderas för varje minut. Om det är högre än per sekund ett genomsnitt över minut, servern kan inte vissa begäranden. SDK buffrar data och ett försök görs att skicka den. Det sprids ut en ökning i över flera minuter. Om din app konsekvent skickar data på mer än den begränsade hastigheten, vissa data att tas bort. (ASP.NET, Java och JavaScript SDK försöker skicka data om det här sättet och andra SDK: er kan bara ta bort begränsas data.) Om begränsning inträffar varnar en varning om meddelande om att det har uppstått.

*Hur vet hur mycket data som skickar min app?*

Du kan använda något av följande alternativ för att se hur mycket data som skickar din app:

* Gå till den **användnings- och uppskattade kostnaden** rutan att visa diagrammet volym dagliga data. 
* Lägg till ett nytt diagram i Metrics Explorer. Diagram-mått, Välj **datapunktsvolym**. Aktivera **gruppering**, och sedan gruppen av **datatyp**.

## <a name="reduce-your-data-rate"></a>Minska vilken takt dina data
Här följer några saker du kan göra för att minska datavolymen:

* Använd [provtagning](app-insights-sampling.md). Den här tekniken minskar vilken takt dina data utan skeva din mått. Du förlorar inte möjlighet att navigera mellan relaterade objekt i sökningen. I server-appar fungerar provtagning automatiskt.
* [Begränsa antalet Ajax-anrop som kan rapporteras](app-insights-javascript.md#detailed-configuration) i varje vyn sida, eller Byt ut Ajax-rapportering.
* [Redigera ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) inaktivera samling moduler som du inte behöver. Du kan till exempel bestämma att prestandaräknare eller beroendedata är inessential.
* Dela din telemetri bland separat instrumentation nycklar. 
* Före aggregera mått. Om du placerar anrop till TrackMetric i din app kan du minska trafik med hjälp av överlagring som accepterar dina beräkning av medelvärdet och standardavvikelsen för en grupp med mått. Du kan också använda en [före sammanställa paketet](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Hantera maximala dagliga datavolym

Du kan använda daglig volym fästpunkten för att begränsa de data som samlas in. Men om fästpunkten är uppfyllt, inträffar en förlust av all telemetri som skickas från ditt program för resten av dagen. Det är *inte tillrådligt* nådde dagliga fästpunkten ha ditt program. Du kan inte spåra hälsotillstånd och prestanda för ditt program när den når dagliga sockeln.

I stället för daglig volym fästpunkten, Använd [provtagning](app-insights-sampling.md) att justera datavolym till önskad nivå. Använd sedan dagliga fästpunkten endast som en ”sista utväg” om programmet plötsligt börjar skicka mycket större mängder telemetri.

Ändra dagliga fästpunkten i den **konfigurera** avsnitt i Application Insights-resurs i den **användning och de uppskattade kostnaderna** väljer **dagliga Cap**.

![Justera dagliga telemetri volym fästpunkten](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Samling
[Provtagning](app-insights-sampling.md) är en metod för att minska frekvensen telemetri har skickats till din app samtidigt behålla möjligheten att hitta relaterade händelser under diagnostiska sökningar. Du behåller även rätt händelse antal.

Sampling är ett effektivt sätt att minska kostnaderna och hålla sig inom den månatliga kvoten. Algoritmen provtagning så behåller relaterade objekt för telemetri, till exempel när du använder Sök hittar du den begäran som rör en viss undantag. Algoritmen behåller även rätt antal så att du kan se de korrekta värdena i måttet Explorer för begäran frekvens, undantag priser och andra antal.

Det finns flera typer av provtagning.

* [Anpassningsbar provtagning](app-insights-sampling.md) är standardinställningen för ASP.NET-SDK. Anpassningsbar provtagning justeras automatiskt telemetrivolym som skickar din app. Det körs automatiskt i SDK i ditt webbprogram så att telemetri nätverkstrafiken minskar. 
* *Införandet provtagning* är ett alternativ som fungerar på den punkt där telemetri från din app kommer in Application Insights-tjänsten. Införandet provtagning påverkar inte telemetrivolym som skickas från din app, men det minskar den volym som behålls av tjänsten. Du kan använda införandet samplingsfrekvensen för att minska den kvot som används telemetri från webbläsare och andra SDK: er.

Om du vill ange införandet provtagning, gå till den **priser** fönstret:

![Välj panelen prover i kvoten och prisnivå rutan, och välj sedan en provtagning bråkdel](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Den **Data provtagning** rutan kontrollerar värdet för införandet provtagning. Samplingsfrekvens som används av Application Insights SDK i din app visas inte. Om inkommande telemetri redan samlats in i SDK, används inte införandet provtagning.
>

För att identifiera faktiska samplingsfrekvensen, oavsett om den har tillämpats, använda en [Analytics-fråga](app-insights-analytics.md). Frågan ser ut så här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Bevaras i varje post, `itemCount` anger antal ursprungliga poster som representerar. Det är lika med 1 + antalet tidigare borttagna poster. 

## <a name="automation"></a>Automation

Du kan skriva ett skript för att ange pris planen med hjälp av Azure Resource Manager. [Lär dig mer](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Nästa steg

* [Sampling](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/