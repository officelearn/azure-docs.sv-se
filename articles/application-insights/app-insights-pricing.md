---
title: "Hantera volymen priser och data för Azure Application Insights | Microsoft Docs"
description: "Hantera telemetri volymer och övervaka kostnader i Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mbullwin
ms.openlocfilehash: 95c5195ac2ea832586211cce37eb2094e06eaf03
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Hantera priser och data volym i Application Insights


Priser för [Azure Application Insights] [ start] baseras på datavolym per program. Låg användning under utvecklingen eller för en liten app kommer troligen att vara fria, eftersom det inte finns en 1 GB månatlig ersättning av telemetridata.

Varje Application Insights-resurs debiteras som en separat tjänst och bidrar till faktura för din prenumeration på Azure.

Det finns två prisnivå planer. Standardplanen kallas Basic. Du kan välja företagsplan som har en daglig kostnad, men gör att ytterligare funktioner som [löpande export](app-insights-export-telemetry.md).

Om du har frågor om hur priser fungerar för Application Insights passa på att skicka en fråga i vår [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-price-plans"></a>Pris-planer

Finns det [Application Insights sida med priser] [ pricing] för aktuella priser i din valuta.

### <a name="basic-plan"></a>Grundläggande planen

Den grundläggande planen är standardinställningen när en ny Application Insights-resurs skapas och räcker för de flesta kunder.

* I den grundläggande planen du debiteras av datavolym: antal byte av telemetri som tagits emot av Application Insights. Datavolymen mäts som storleken på okomprimerade JSON-datapaketet som tagits emot av Application Insights från ditt program.
För [tabelldata importeras till Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datavolym mäts som den okomprimerade storleken för filer som skickats till Application Insights.  
* Din första 1 GB för varje app är ledigt, så om du bara experimentera eller utveckla kan du inte antagligen kan få betala.
* [Live mått dataströmmen](app-insights-live-stream.md) data inte räknas för prissättning.
* [Löpande Export](app-insights-export-telemetry.md) är tillgänglig för en extra per GB-tillägget i den grundläggande planen.

### <a name="enterprise-plan"></a>Företagsplan

* Din app kan använda alla funktioner i Application Insights i Enterprise-abonnemang. [Löpande Export](app-insights-export-telemetry.md) och 

[Logga Analytics connector](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) finns utan någon extra kostnad i Enterprise-abonnemang.
* Du betalar per nod som skickar telemetri för alla program i Enterprise-abonnemang. 
 * En *nod* är en fysisk eller virtuell server-dator eller en plattform som en tjänst-rollinstans som är värd för din app.
 * Datorer för utveckling, klientwebbläsare och mobila enheter räknas inte som noder.
 * Om din app har flera komponenter som skickar telemetri, till exempel en webbtjänst och en serverdel worker räknas de separat.
 * [Live mått dataströmmen](app-insights-live-stream.md) data inte räknas för priser purposes.* över en prenumeration är dina avgifter per nod inte per app. Om du har fem noder skicka telemetri för 12 appar och sedan tillägget är de fem noder.
* Även om avgifter citattecken per månad, använder du debiteras endast för varje timme som en nod skickar telemetri från en app. Varje timme tillägget är inom citattecken månadskostnaden / 744 (antal timmar i 31 dagar i månaden).
* En data-volym tilldelning av 200 MB per dag anges för varje nod som identifierades (med timme). Allokering av oanvända data överförs inte från en dag till nästa.
 * Om du väljer alternativet Enterprise prisnivå får varje prenumeration en daglig ersättning av data baserat på antalet noder som skickas telemetri till Application Insights-resurser i den prenumerationen. Så om du har 5 och skicka data alla dag-noder har en pool ersättning på 1 GB som tillämpas på alla Application Insights-resurser i den prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom inkluderade data delas mellan alla noder. Om en viss dag Application Insights-resurser visas mer data än vad som ingår i den dagliga data allokeringen för den här prenumerationen gäller överförbrukning data-avgifter per GB. 
 * Dagliga data ersättningen beräknas som antalet timmar på dagen (med UTC) att varje nod skickar telemetri divideras med 24 gånger 200 MB. Så om du har 4 noder skicka telemetri under 15 i 24 timmar på dagen, med data för den dagen skulle vara ((4 x 15) / 24) x 200 MB = 500 MB. Till pris 2.30 USD per GB för data överförbrukning är för 1,15 USD om noderna skickar 1 GB data den dagen.
 * Observera att den företagsplan dagliga ersättningen inte delas med program som du har valt alternativet grundläggande och oanvända ersättning överförs inte från dagliga. 
* Här följer några exempel för att avgöra antalet distinkta noder:
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
  * Observera att om ditt program använder SDK för att ange roleInstance till ett anpassat värde som standard samma värde används för att fastställa antalet noder. 
  * Om du använder en ny version av SDK med en app som körs från klientdatorer eller mobila enheter, är det möjligt att antalet noder kan returnera ett tal som är mycket stora (från stort antal klientdatorer eller mobilenheter). 

### <a name="multi-step-web-tests"></a>Webbtester med flera steg

Det finns en extra kostnad för [webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Detta refererar till webbtester som utför en sekvens med åtgärder. 

Det kostar ingenting separat för Pingtest av en enstaka sida. Telemetri från både Pingtest och flera steg tester debiteras tillsammans med andra telemetri från din app.
 
## <a name="operations-management-suite-subscription-entitlement"></a>Operations Management Suite-prenumeration rätt

Som [presenterade nyligen](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), kunder som köper Microsoft Operations Management Suite E1 och E2 ska kunna hämta Application Insights Enterprise som en ytterligare komponent utan extra kostnad. Varje enhet Operations Management Suite E1 och E2 innehåller främst rätt att 1 nod i Enterprise-planen i Application Insights. Som nämnts ovan innehåller varje nod för Application Insights upp till 200 MB data som inhämtas per dag (separat från logganalys datapåfyllning) med 90 dagars data bevaras utan extra kostnad. 

> [!NOTE]
> För att säkerställa att du får denna rättighet, måste du ha Application Insights-resurser i företaget priser plan. Denna rättighet gäller endast som noder, så inga fördelar inte uppnår med Application Insights-resurser i den grundläggande planen. Observera att denna rättighet visas inte på de uppskattade kostnaderna visas på funktioner + prisnivå bladet. 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>Granska prisnivå planer och uppskatta kostnader

Application Insights gör det enkelt att förstå de prisnivå tillgängliga planerna och vad kostnaderna sannolikt baseras på senaste användningsmönster. Starta genom att öppna den **funktioner + priser** bladet i Application Insights-resurs i Azure-portalen:

![Välj prisnivå.](./media/app-insights-pricing/01-pricing.png)

**a.** Granska dina datavolym för månaden. Detta omfattar alla data tas emot och behålls (efter något [provtagning](app-insights-sampling.md) från din server och -klientprogram och tillgänglighetstester.

**b.** En separat kostnad görs efter [webbtester med flera steg](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Detta innehåller enkla tillgänglighetstester som ingår i data volym kostnad.)

**c.** Aktivera Enterprise-plan.

**d.** Klicka här för att data management alternativ för att visa datavolym för den senaste månaden, ange en daglig fjärrskrivbordsanslutning eller ange införandet provtagning.

Application Insights avgifter läggs till fakturan Azure. Du kan se information om din Azure debiterar i avsnittet faktureringen av Azure-portalen eller i den [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![På menyn sida väljer du fakturering.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Datahastighet
Det finns tre sätt som den volym som du skickar data begränsas:

* **Provtagning:** kan användas för den här mekanismen minska mängden telemetri som skickas från din server och klient appar, med minimal förvrängning av mått. Detta är det viktigaste verktyget måste du justera mängden data. Lär dig mer om [provtagning funktioner](app-insights-sampling.md). 
* **Dagliga cap:** när skapar en Application Insights-resurs i Azure Portal denna är inställd på 100 GB/dag. Standard när du skapar en resurs för Application Insights från Visual Studio är liten (endast 32,3 MB/dag) som används för att underlätta testningen. I det här fallet är avsedda att användaren höjer dagliga fästpunkten innan du distribuerar appen till produktionen. Högsta kapacitet är 1 000 GB/dag om du har begärt en högre maximal för ett program med hög trafik. Försiktig när du ställer in dagliga fästpunkten som du vill ska vara **aldrig att träffa dagliga fästpunkten**, eftersom du kommer att förlora data för resten av dagen och går inte att övervaka programmet. Använd daglig volym cap bladet länkas från bladet hantering av volymen (se nedan) för att ändra den. Observera att vissa prenumerationstyper av kredit som inte kan användas för Application Insights. Om prenumerationen har en utgiftsgräns, har bladet dagliga cap instruktioner ta bort den och aktivera dagliga fästpunkten höjas utöver 32,3 MB per dag.  
* **Begränsning:** detta begränsar datahastighet till 32 kB händelser per sekund, ett genomsnitt över 1 minut. 


*Vad händer om min app överskrider den begränsade hastigheten?*

* Mängden data som appen skickar utvärderas för varje minut. Om det är högre än per sekund ett genomsnitt över minut, servern kan inte vissa begäranden. SDK buffrar data och ett försök görs att skicka, sprida en ökning i över flera minuter. Om din app konsekvent skickar data på ovanför den begränsade hastigheten, vissa data att tas bort. (ASP.NET, Java och JavaScript SDK försöker skicka om på det här sättet och andra SDK: er kan bara ta bort begränsas data.) Om begränsning inträffar, visas ett meddelande som varning detta har skett.

*Hur vet hur mycket data som skickar min app?*

* Öppna den **volym datahantering** bladet för att se diagrammet volym dagliga data. 
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

Om du vill ändra dagliga fästpunkten i avsnittet Konfigurera av Application Insights-resurs klickar du på **volym datahantering** sedan **dagliga Cap**.

![Justera dagliga telemetri volym fästpunkten](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>Samling
[Provtagning](app-insights-sampling.md) är en metod för att minska frekvensen telemetri har skickats till din app samtidigt behålla möjligheten att hitta relaterade händelser under diagnostiska sökningar och räknar fortfarande behåller rätt händelse. 

Sampling är ett effektivt sätt att minska kostnaderna och hålla sig inom den månatliga kvoten. Algoritmen provtagning behåller relaterade objekt för telemetri, så att till exempel när du använder Sök hittar du den begäran som rör en viss undantag. Algoritmen behåller även rätt antal så att du ser rätt värden i måttet Explorer för begäran om priser, undantag priser och andra antal.

Det finns flera typer av provtagning.

* [Anpassningsbar provtagning](app-insights-sampling.md) är standardinställningen för SDK för ASP.NET, som automatiskt justerar telemetrivolym som skickar din app. Det körs automatiskt i SDK i ditt webbprogram, så att minskar telemetri trafik i nätverket. 
* *Införandet provtagning* är ett alternativ som fungerar på den punkt där telemetri från din app kommer in Application Insights-tjänsten. Det påverkar inte telemetrivolym som skickas från din app, men det minskar den volym som behålls av tjänsten. Du kan använda den för att minska kvoten används av telemetri från webbläsare och andra SDK: er.

Ange kontrollen i bladet priser för att ange införandet provtagning:

![Kvoten och prisnivå bladet, klickar du på panelen prover och välj provtagning decimaltal.](./media/app-insights-pricing/04.png)

> [!WARNING]
> Bladet Data provtagning styr endast värdet för införandet provtagning. Samplingsfrekvens som används av Application Insights SDK i din app visas inte. Om inkommande telemetri redan samlats in på SDK, tillämpas inte införandet provtagning.
> 

För att identifiera den faktiska samplingsfrekvensen oavsett om den har tillämpats, använda en [Analytics query](app-insights-analytics.md) sådana här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Bevaras i varje post, `itemCount` anger antal ursprungliga poster som representerar, lika med 1 + antalet tidigare borttagna poster. 


## <a name="automation"></a>Automation

Du kan skriva ett skript för att ange prisplan med Azure Resource Manager. [Lär dig hur](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Sammanfattning av gränser
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Nästa steg

* [Sampling](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

