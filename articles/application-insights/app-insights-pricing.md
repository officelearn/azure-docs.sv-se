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
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Hantera priser och data volym i Application Insights

Priser för [Azure Application Insights] [ start] baseras på datavolym per program. Varje Application Insights-resurs debiteras som en separat tjänst och bidrar till faktura för din prenumeration på Azure.

Det finns två prisnivå planer. Standardplanen kallas Basic som innehåller alla samma funktioner som företagsplan på utan ytterligare kostnad och växlar i första hand på mängden data som inhämtas. Om du använder Operations Management Suite, bör du välja företaget plan som har en per nod debiterar tillsammans med dagligen data bidrag och debiteras för data som inhämtas ovan ingår ersättningen.

Om du har frågor om hur priser fungerar för Application Insights passa på att skicka en fråga i vår [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Pris-planer

Finns det [Application Insights sida med priser] [ pricing] för aktuella priser i din valuta och region.

### <a name="basic-plan"></a>Grundläggande planen

Den grundläggande planen är standardinställningen när en ny Application Insights-resurs har skapats och är optimala för alla kunder utom de med en Operations Management Suite-prenumeration.

* I den grundläggande planen du debiteras av datavolym: antal byte av telemetri som tagits emot av Application Insights. Datavolymen mäts som storleken på okomprimerade JSON-datapaketet som tagits emot av Application Insights från ditt program.
För [tabelldata importeras till Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datavolym mäts som den okomprimerade storleken för filer som skickats till Application Insights.
* [Live mått dataströmmen](app-insights-live-stream.md) data inte räknas för prissättning.
* [Löpande Export](app-insights-export-telemetry.md) och [logganalys connector](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) är tillgängliga utan någon extra kostnad i den grundläggande planen från och med April 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Företagsplan och rättigheter för Operations Management Suite-prenumeration

Kunder som köper Microsoft Operations Management Suite E1 och E2 ska kunna hämta Application Insights Enterprise som en ytterligare komponent utan extra kostnad som [tidigare meddelats](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Varje enhet Operations Management Suite E1 och E2 innehåller främst rätt att 1 nod i Enterprise-planen i Application Insights. Som beskrivs nedan i detalj varje nod för Application Insights innehåller upp till 200 MB data som inhämtas per dag (separat från logganalys datapåfyllning) med 90 dagars data bevaras utan extra kostnad. Eftersom detta gäller endast för kunder med en Operations Management Suite-prenumeration, utan någon prenumeration kommer inte att se välja den här planen.

> [!NOTE]
> För att säkerställa att du får denna rättighet, måste du ha Application Insights-resurser i företaget priser plan. Denna rättighet gäller endast som noder, så inga fördelar inte uppnår med Application Insights-resurser i den grundläggande planen. Observera att denna rättighet visas inte på de uppskattade kostnaderna visas på den *användnings- och uppskattade kostnaden* sidan. Även om du flyttar en prenumeration till nya Azure övervakning prissättningsmodellerna för April 2018 grundläggande planen kommer att vara tillgänglig endast planen så inte är det bäst om du har en Operations Management Suite-prenumeration.

Mer information om Enterprise-planen finns i [Enterprise prisnivå informationssidan.](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>Webbtester med flera steg

Det finns en extra kostnad för [webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Detta refererar till webbtester som utför en sekvens med åtgärder.

Det kostar ingenting separat för Pingtest av en enstaka sida. Telemetri från både Pingtest och flera steg tester debiteras tillsammans med andra telemetri från din app.

## <a name="review-pricing-plans-and-estimate-costs"></a>Granska prisnivå planer och uppskatta kostnader

Application Insights gör det enkelt att förstå prisnivå planerna som är tillgängliga och vad kostnaderna sannolikt kommer att baseras på senaste användningsmönster. Starta genom att öppna den **användning och de uppskattade kostnaderna** sida i Application Insights-resurs i Azure-portalen:

![Välj prisnivå.](./media/app-insights-pricing/pricing-001.png)

**a.** Granska dina datavolym för månaden. Detta omfattar alla data tas emot och behålls (efter något [provtagning](app-insights-sampling.md) från din server och -klientprogram och tillgänglighetstester.

**b.** En separat kostnad görs efter [webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Detta innehåller enkla tillgänglighetstester som ingår i data volym kostnad.)

**c.** Visa datatrender för den senaste månaden.

**d.** Aktivera datapåfyllning [provtagning.](app-insights-sampling.md) 

**e.** Konfigurera dagliga data volym sockeln.

Application Insights avgifter läggs till fakturan Azure. Du kan se information om din Azure debiterar i avsnittet faktureringen av Azure-portalen eller i den [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![På menyn sida väljer du fakturering.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Datahastighet
Det finns tre sätt som den volym som du skickar data begränsas:

* **Provtagning:** kan användas för den här mekanismen minska mängden telemetri som skickas från din server och klient appar, med minimal förvrängning av mått. Detta är det viktigaste verktyget måste du justera mängden data. Lär dig mer om [provtagning funktioner](app-insights-sampling.md). 
* **Dagliga cap:** när skapar en Application Insights-resurs i Azure Portal denna är inställd på 100 GB/dag. Standard när du skapar en resurs för Application Insights från Visual Studio är liten (endast 32,3 MB/dag) som används för att underlätta testningen. I det här fallet är avsedda att användaren höjer dagliga fästpunkten innan du distribuerar appen till produktionen. Högsta kapacitet är 1 000 GB/dag om du har begärt en högre maximal för ett program med hög trafik. Försiktig när du ställer in dagliga fästpunkten som du vill ska vara **aldrig att träffa dagliga fästpunkten**, eftersom du kommer att förlora data för resten av dagen och går inte att övervaka programmet. Använd daglig volym cap alternativet om du vill ändra det länkade från användnings- och uppskattade kostnaderna sida (se nedan). Vi har tagit bort begränsningen på vissa prenumerationstyper av som har kredit som inte kan användas för Application Insights. Tidigare, om prenumerationen har en utgiftsgräns, dialogrutan dagliga cap har instruktioner ta bort den och aktivera dagliga fästpunkten höjas utöver 32,3 MB per dag.
* **Begränsning:** detta begränsar datahastighet 32 000 händelser per sekund, genomsnittlig över 1 minut.

*Vad händer om min app överskrider den begränsade hastigheten?*

* Mängden data som appen skickar utvärderas för varje minut. Om det är högre än per sekund ett genomsnitt över minut, servern kan inte vissa begäranden. SDK buffrar data och ett försök görs att skicka, sprida en ökning i över flera minuter. Om din app konsekvent skickar data på ovanför den begränsade hastigheten, vissa data att tas bort. (ASP.NET, Java och JavaScript SDK försöker skicka om på det här sättet och andra SDK: er kan bara ta bort begränsas data.) Om begränsning inträffar, visas ett meddelande som varning detta har skett.

*Hur vet hur mycket data som skickar min app?*

* Öppna den **användnings- och uppskattade kostnaden** sidan för att visa diagrammet volym dagliga data. 
* I Metrics Explorer lägga till ett nytt schema eller välj **datapunktsvolym** som dess mått. Växla på gruppering och gruppera efter **datatyp**.

## <a name="to-reduce-your-data-rate"></a>Kan minska dina data
Här följer några saker du kan göra för att minska datavolymen:

* Använd [provtagning](app-insights-sampling.md). Den här tekniken minskar datahastighet utan skeva din mått och utan att störa möjlighet att navigera mellan relaterade objekt i sökningen. I server appar fungerar den automatiskt.
* [Begränsa antalet Ajax-anrop som kan rapporteras](app-insights-javascript.md#detailed-configuration) i varje vyn sida, eller Byt ut Ajax-rapportering.
* Stäng av samlingen moduler som du inte behöver genom [redigera ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Du kan till exempel bestämma att prestandaräknare eller beroendedata är inessential.
* Dela din telemetri för att avgränsa instrumentation nycklar. 
* Före aggregera mått. Om du har placerat anrop till TrackMetric i din app, kan du minska trafik med hjälp av överlagring som accepterar dina beräkning av medelvärdet och standardavvikelsen för en grupp med mått. Du kan också använda en [före sammanställa paketet](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Hantera maximala dagliga datavolym

Du kan använda daglig volym fästpunkten för att begränsa de data som samlas in, men om fästpunkten är uppfyllt, det leder till förlust av all telemetri som skickas från ditt program för resten av dagen. Det är **inte tillrådligt** har programmet att träffa dagliga fästpunkten eftersom det inte går att spåra hälsotillstånd och prestanda för programmet när det med samma namn.

Använd i stället [provtagning](app-insights-sampling.md) för att justera datavolym till nivån som du skulle och dagliga fästpunkten endast som en ”sista utväg” om ditt program börjar skicka mycket större mängder telemetri oväntat.

Ändra dagliga fästpunkten i avsnittet Konfigurera av Application Insights-resursen från den **användning och de uppskattade kostnaderna** klickar du på **dagliga Cap**.

![Justera dagliga telemetri volym fästpunkten](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Samling
[Provtagning](app-insights-sampling.md) är en metod för att minska frekvensen telemetri har skickats till din app samtidigt behålla möjligheten att hitta relaterade händelser under diagnostiska sökningar och räknar fortfarande behåller rätt händelse.

Sampling är ett effektivt sätt att minska kostnaderna och hålla sig inom den månatliga kvoten. Algoritmen provtagning behåller relaterade objekt för telemetri, så att till exempel när du använder Sök hittar du den begäran som rör en viss undantag. Algoritmen behåller även rätt antal så att du ser rätt värden i måttet Explorer för begäran om priser, undantag priser och andra antal.

Det finns flera typer av provtagning.

* [Anpassningsbar provtagning](app-insights-sampling.md) är standardinställningen för SDK för ASP.NET, som automatiskt justerar telemetrivolym som skickar din app. Det körs automatiskt i SDK i ditt webbprogram, så att minskar telemetri trafik i nätverket. 
* *Införandet provtagning* är ett alternativ som fungerar på den punkt där telemetri från din app kommer in Application Insights-tjänsten. Det påverkar inte telemetrivolym som skickas från din app, men det minskar den volym som behålls av tjänsten. Du kan använda den för att minska kvoten används av telemetri från webbläsare och andra SDK: er.

Om du vill ange införandet provtagning, anger du kontrollen i dialogrutan priser:

![Kvoten och prisnivå dialogrutan, klickar du på panelen prover och välj provtagning decimaltal.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Dialogrutan Data provtagning styr endast värdet för införandet provtagning. Samplingsfrekvens som används av Application Insights SDK i din app visas inte. Om inkommande telemetri redan samlats in på SDK, tillämpas inte införandet provtagning.
>

För att identifiera den faktiska samplingsfrekvensen oavsett om den har tillämpats, använda en [Analytics query](app-insights-analytics.md) sådana här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Bevaras i varje post, `itemCount` anger antal ursprungliga poster som representerar, lika med 1 + antalet tidigare borttagna poster. 

## <a name="automation"></a>Automatisering

Du kan skriva ett skript för att ange prisplan med Azure Resource Manager. [Lär dig mer](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Nästa steg

* [Sampling](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
