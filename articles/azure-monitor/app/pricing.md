---
title: Hantera användning och kostnader för Azure Application Insights | Microsoft-dokument
description: Hantera telemetrivolymer och övervaka kostnader i Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: b782477fd29b34eda70813fc2aff29157f02acb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275950"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Hantera användning och kostnader för Application Insights

> [!NOTE]
> I den här artikeln beskrivs hur du förstår och styr dina kostnader för Application Insights.  En relaterad artikel, [Övervakning av användning och uppskattade kostnader](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) beskriver hur du visar användning och uppskattade kostnader över flera Azure-övervakningsfunktioner för olika prismodeller.

Application Insights är utformat för att få allt du behöver för att övervaka tillgänglighet, prestanda och användning av dina webbprogram, oavsett om de finns på Azure eller lokalt. Application Insights stöder populära språk och ramverk, till exempel .NET, Java och Node.js, och integreras med DevOps-processer och verktyg som Azure DevOps, Jira och PagerDuty. Det är viktigt att förstå vad som avgör kostnaderna för att övervaka dina program. I den här artikeln granskar vi vad som driver dina kostnader för programövervakning och hur du proaktivt kan övervaka och kontrollera dem.

Om du har frågor om hur prissättning fungerar för Application Insights kan du ställa en fråga i vårt [forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Prismodell

Prissättningen för [Azure Application Insights][start] är en **Pay-As-You-Go-modell** baserad på datavolym som intas och eventuellt för längre datalagring. Varje Application Insights-resurs debiteras som en separat tjänst och bidrar till fakturan för din Azure-prenumeration. Datavolymen mäts som storleken på det okomprimerade JSON-datapaketet som tas emot av Application Insights från ditt program. Det finns ingen datavolymavgift för att använda [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

[Webbtester](../../azure-monitor/app/availability-multistep.md) i flera steg medför en extra kostnad. Webbtester i flera steg är webbtester som utför en sekvens av åtgärder. Det finns ingen separat avgift för *ping tester* av en enda sida. Telemetri från ping-tester och flerstegstester debiteras på samma sätt som annan telemetri från din app.

## <a name="estimating-the-costs-to-manage-your-application"></a>Uppskatta kostnaderna för att hantera ditt program

Om du ännu inte använder Application Insights kan du använda [Azure Monitor-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att uppskatta kostnaden för att använda Application Insights. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor-panelen. Bläddra nedåt på sidan till Azure Monitor och välj Programstatistik i listrutan Typ.  Här kan du ange antalet GB data som du förväntar dig att samla in per månad, så frågan är hur mycket data som Application Insights samlar in för att övervaka ditt program.

Det finns två metoder för att ta itu med detta: användning av standardövervakning och adaptiv sampling, som är tillgänglig i ASP.NET SDK, eller uppskatta ditt sannolika datainmatning baserat på vad andra liknande kunder har sett.

### <a name="data-collection-when-using-sampling"></a>Datainsamling vid användning av provtagning

Med ASP.NET [SDK:s adaptiva sampling](sampling.md#adaptive-sampling)justeras datavolymen automatiskt för att hålla sig inom en angiven maximal trafikhastighet för standardövervakning av Application Insights. Om programmet producerar en låg mängd telemetri, till exempel när felsökning eller på grund av låg användning, kommer objekten inte att tas bort av samplingsprocessorn så länge volymen ligger under de konfigurerade händelserna per sekund. För ett program med hög volym, med standardtröskeln på fem händelser per sekund, begränsar adaptiv sampling antalet dagliga händelser till 432 000. Med en typisk genomsnittlig händelsestorlek på 1 KB motsvarar detta 13,4 GB telemetri per 31-dagarsmånad per nod som är värd för ditt program (eftersom samplingen görs lokalt för varje nod.) 

För SDK:er som inte stöder adaptiv sampling kan du använda [inmatningsprovtagning](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), som tar prover när data tas emot av Application Insights baserat på en procentandel data att behålla, eller [fast sampling för ASP.NET, ASP.NET Core och Java-webbplatser](sampling.md#fixed-rate-sampling) för att minska trafiken som skickas från webbservern och webbläsare

### <a name="learn-from-what-similar-customers-collect"></a>Lär av vad liknande kunder samlar in

Om du aktiverar funktionen Uppskatta datavolym baserat på programaktivitet i Azure Monitoring Pricing calculator for Application Insights kan du ange indata om ditt program (begäranden per månad och sidvisningar per månad, om du gör det samla in telemetri på klientsidan), och sedan kommer räknaren att berätta median- och 90:e percentilens mängd data som samlas in av liknande program. Dessa program sträcker sig över intervallet Application Insights konfiguration (t.ex. vissa har [standardprovtagning](../../azure-monitor/app/sampling.md), vissa har ingen sampling etc.), så du har fortfarande kontroll för att minska mängden data som du intar långt under mediannivån med hjälp av sampling. Men detta är en utgångspunkt för att förstå vad andra, liknande kunder ser.

## <a name="understand-your-usage-and-estimate-costs"></a>Förstå dina användnings- och uppskattningskostnader

Application Insights gör det enkelt att förstå vad dina kostnader sannolikt kommer att baseras på de senaste användningsmönstren. För att komma igång, i Azure-portalen, för Application Insights-resursen, gå till sidan **Användning och uppskattade kostnader:**

![Välj priser](./media/pricing/pricing-001.png)

A. Granska din datavolym för månaden. Detta inkluderar alla data som tas emot och lagras (efter [eventuella samplingar)](../../azure-monitor/app/sampling.md)från server- och klientapparna och från tillgänglighetstester.  
B. En separat laddning görs för [webbtester i flera steg.](../../azure-monitor/app/availability-multistep.md) (Detta inkluderar inte enkla tillgänglighetstester som ingår i datavolymavgiften.)  
C. Visa datavolymtrender för den senaste månaden.  
D. Aktivera [datainmatningsprovtion](../../azure-monitor/app/sampling.md).
E. Ställ in det dagliga datavolymtaket.  

(Observera att alla priser som visas i skärmbilder i den här artikeln endast är avsedda som exempel. För aktuella priser i din valuta och region, se [Application Insights prissättning][pricing].)

Om du vill undersöka användningen av Application Insights djupare öppnar du sidan **Mått,** lägger till måttet "Datapunktsvolym" och väljer sedan alternativet *Använd delning* för att dela upp data efter "Telemetriobjekttyp".

Avgifter för application insights läggs till i din Azure-faktura. Du kan se information om din Azure-faktura i avsnittet **Fakturering** i Azure-portalen eller i [Azure-faktureringsportalen](https://account.windowsazure.com/Subscriptions).

![Välj Fakturering på den vänstra menyn](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Använda datavolymmått
<a id="understanding-ingested-data-volume"></a>

Om du vill veta mer om dina **datavolymer** lägger du till ett nytt diagram genom att välja Mått för application insights-resursen. Välj **Datapunktsvolym**under Loggbaserade mått under **Loggbaserade mått**. Klicka på **Använd delning**och välj grupp efter ** `Telemetryitem` typ**.

![Använda mått för att titta på datavolymen](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Frågor för att förstå information om datavolym

Det finns två metoder för att undersöka datavolymer för Application Insights. Den första använder aggregerad information i `systemEvents` `_BilledSize` tabellen och den andra använder egenskapen, som är tillgänglig för varje intas händelse.

#### <a name="using-aggregated-data-volume-information"></a>Använda aggregerad datavolyminformation

Du kan till exempel `systemEvents` använda tabellen för att se datavolymen som förtärs under de senaste 24 timmarna med frågan:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Eller om du vill visa ett diagram över datavolymen (i byte) efter datatyp under de senaste 30 dagarna kan du använda:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Observera att den här frågan kan användas i en [Azure Log Alert](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) för att ställa in aviseringar om datavolymer.  

Om du vill veta mer om dina ändringar av telemetridata kan vi få antalet händelser efter typ med hjälp av frågan:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Använda datastorlek per händelseinformation

Om du vill veta mer om källan till `_BilledSize` dina datavolymer kan du använda egenskapen som finns på varje intvald händelse.

Om du till exempel vill titta på vilka åtgärder som genererar mest `_BilledSize` datavolym under de senaste 30 dagarna kan vi summera för alla beroendehändelser:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visa användning av programstatistik på din Azure-faktura

Azure tillhandahåller en hel del användbara funktioner i [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hub. Med funktionen Kostnadsanalys kan du till exempel visa dina utgifter för Azure-resurser. Genom att lägga till ett filter efter resurstyp (i microsoft.insights/components for Application Insights) kan du spåra dina utgifter.

Mer förståelse för din användning kan vinnas genom [att ladda ner din användning från Azure-portalen](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal).
I det hämtade kalkylbladet kan du se användning per Azure-resurs per dag. I det här Excel-kalkylbladet kan användning från dina Application Insights-resurser hittas genom att först filtrera i kolumnen "Mätarkategori" för att visa "Application Insights" och "Log Analytics", och sedan lägga till ett filter i kolumnen "Instans-ID" som "innehåller microsoft.insights/components".  De flesta application insights-användning rapporteras på mätare med mätarkategorin Log Analytics, eftersom det finns en enda loggar backend för alla Azure Monitor-komponenter.  Endast Application Insights-resurser på äldre prisnivåer och webbtester i flera steg rapporteras med en mätarkategori för application insights.  Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "Enhet".  Mer information finns tillgänglig för att hjälpa dig att [förstå din Microsoft Azure-faktura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="managing-your-data-volume"></a>Hantera datavolymen

Mängden data som du skickar kan hanteras med följande tekniker:

* **Sampling**: Du kan använda sampling för att minska mängden telemetri som skickas från servern och klientapparna, med minimal förvrängning av mått. Sampling är det primära verktyget som du kan använda för att justera mängden data som du skickar. Läs mer om [samplingsfunktioner](../../azure-monitor/app/sampling.md).

* **Begränsa Ajax samtal:** Du kan [begränsa antalet Ajax samtal som kan rapporteras](../../azure-monitor/app/javascript.md#configuration) i varje sidvisning, eller stänga av Ajax rapportering.

* **Inaktivera onödiga moduler:** [Redigera ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) för att inaktivera samlingsmoduler som du inte behöver. Du kan till exempel bestämma att prestandaräknare eller beroendedata är ouppnämliga.

* **Föraggregerade mått:** Om du lägger till samtal till TrackMetric i din app kan du minska trafiken genom att använda överbelastningen som accepterar din beräkning av medelvärdet och standardavvikelsen för en grupp mätningar. Du kan också använda ett [föraggande paket](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Dagligt tak**: När du skapar en Application Insights-resurs i Azure-portalen är det dagliga taket inställt på 100 GB/dag. När du skapar en Application Insights-resurs i Visual Studio är standardvärdet litet (endast 32,3 MB/dag). Den dagliga tak standard är inställd på att underlätta testning. Det är tänkt att användaren kommer att höja det dagliga taket innan du distribuerar appen i produktion. 

    Det maximala taket är 1 000 GB/dag om du inte begär ett högre maximum för ett program med hög trafik.
    
    Varningsmeddelanden om det dagliga taket skickas till konto som är medlemmar i dessa roller för din Application Insights-resurs: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Ägare".

    Var försiktig när du ställer in det dagliga locket. Din avsikt bör vara att *aldrig träffa den dagliga mössan.* Om du träffar det dagliga taket förlorar du data för resten av dagen och du kan inte övervaka ditt program. Om du vill ändra det dagliga locket använder du alternativet **Dagligt volymtak.** Du kan komma åt det här alternativet i fönstret **Användning och uppskattade kostnader** (detta beskrivs mer i detalj senare i artikeln).
    
    Vi har tagit bort begränsningen för vissa prenumerationstyper som har kredit som inte kunde användas för Application Insights. Tidigare, om prenumerationen har en utgiftsgräns, har den dagliga cap-dialogrutan instruktioner för att ta bort utgiftsgränsen och göra det möjligt att höja det dagliga taket efter 32,3 MB/dag.
    
* **Begränsning:** Begränsning begränsar datahastigheten till 32 000 händelser per sekund, i genomsnitt över 1 minut per instrumenteringsnyckel. Mängden data som appen skickar utvärderas varje minut. Om den överskrider den genomsnittliga skattesatsen per sekund under minuten, avvisar servern vissa begäranden. SDK buffrar data och försöker sedan skicka den igen. Det sprider sig ut en våg över flera minuter. Om din app konsekvent skickar data med mer än begränsningshastigheten tas vissa data bort. (De ASP.NET, Java och JavaScript-SDK:erna försöker skicka data igen på det här sättet, andra SDK:er kan helt enkelt släppa strypta data.) Om begränsning inträffar varnar en varning om att detta har inträffat.

## <a name="manage-your-maximum-daily-data-volume"></a>Hantera din maximala dagliga datavolym

Du kan använda det dagliga volymtaket för att begränsa de insamlade data. Men om locket är uppfyllt inträffar en förlust av all telemetri som skickas från din ansökan under resten av dagen. Det är *inte tillrådligt* att låta din ansökan träffa det dagliga locket. Du kan inte spåra programmets hälsa och prestanda när det når det dagliga locket.

I stället för att använda det dagliga volymtaket använder du [sampling](../../azure-monitor/app/sampling.md) för att justera datavolymen till den nivå du vill ha. Använd sedan det dagliga locket endast som en "sista utväg" om ditt program oväntat börjar skicka mycket högre volymer telemetri.

### <a name="identify-what-daily-data-limit-to-define"></a>Identifiera vilken daglig datagräns som ska definieras

Granska Application Insights Användning och uppskattade kostnader för att förstå datainmatningstrenden och vad som är det dagliga volymtaket att definiera. Det bör övervägas med försiktighet, eftersom du inte kommer att kunna övervaka dina resurser efter att gränsen har nåtts.

### <a name="set-the-daily-cap"></a>Ställ in den dagliga tak

Om du vill ändra det dagliga taket väljer du **Dagligt tak**i avsnittet **Konfigurera** i application insights-resursen. **Usage and estimated costs**

![Justera det dagliga volymlocket för telemetri](./media/pricing/pricing-003.png)

Om du vill [ändra det dagliga taket via Azure Resource Manager](../../azure-monitor/app/powershell.md)är egenskapen som ska ändras `dailyQuota`.  Via Azure Resource Manager kan `dailyQuotaResetTime` du också ställa `warningThreshold`in och det dagliga takets .

### <a name="create-alerts-for-the-daily-cap"></a>Skapa aviseringar för Daily Cap

Den dagliga programstatistiken skapar en händelse i Azure-aktivitetskog när de intas datavolymerna når varningsnivån eller den dagliga taknivån.  Du kan [skapa en avisering baserat på dessa aktivitetslogghändelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal). Signalnamnen för dessa händelser är:

* Varningströskel för Application Insights-komponent dagliga tak har uppnåtts

* Application Insights komponent dagliga tak nås

## <a name="sampling"></a>Samling
[Sampling](../../azure-monitor/app/sampling.md) är en metod för att minska hastigheten med vilken telemetri skickas till din app, samtidigt som du behåller möjligheten att hitta relaterade händelser under diagnostiska sökningar. Du behåller också korrekta händelseantal.

Sampling är ett effektivt sätt att minska avgifterna och hålla sig inom din månatliga kvot. Samplingsalgoritmen behåller relaterade poster av telemetri, så att du till exempel när du använder Sök kan hitta begäran som är relaterad till ett visst undantag. Algoritmen behåller också korrekta antal så att du ser rätt värden i Metric Explorer för begärandensgrader, undantagshastigheter och andra antal.

Det finns flera former av provtagning.

* [Adaptiv sampling](../../azure-monitor/app/sampling.md) är standard för ASP.NET SDK. Adaptiv sampling anpassar sig automatiskt till volymen av telemetri som appen skickar. Den fungerar automatiskt i SDK i webbappen så att telemetritrafiken i nätverket minskar. 
* *Inmatningsprovtagning* är ett alternativ som fungerar vid den punkt där telemetri från din app går in i application insights-tjänsten. Inmatningsprovtagning påverkar inte volymen av telemetri som skickas från din app, men det minskar volymen som behålls av tjänsten. Du kan använda inmatningsprovtion för att minska kvoten som används av telemetri från webbläsare och andra SDK:er.

Om du vill ange inmatningsprovtagning går du till **fönstret Prissättning:**

![I fönstret Kvot och prissättning markerar du panelen Exempel och väljer sedan en samplingsfraktion](./media/pricing/pricing-004.png)

> [!WARNING]
> Fönstret **Dataprovtagning** styr bara värdet för inmatningsprovtagning. Den återspeglar inte samplingsfrekvensen som tillämpas av Application Insights SDK i appen. Om den inkommande telemetrin redan har samplats i SDK används inte intagsprovtagning.
>

Om du vill ta reda på den faktiska samplingsfrekvensen, oavsett var den har tillämpats, använder du en [Analytics-fråga](analytics.md). Frågan ser ut så här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

I varje behållen post `itemCount` anger antalet ursprungliga poster som den representerar. Det är lika med 1 + antalet tidigare kasserade poster.

## <a name="change-the-data-retention-period"></a>Ändra kvarhållningsperioden för data

Standardlagringen för Application Insights-resurser är 90 dagar. Olika kvarhållningsperioder kan väljas för varje Application Insights-resurs. Den fullständiga uppsättningen tillgängliga lagringsperioder är 30, 60, 90, 120, 180, 270, 365, 550 eller 730 dagar.

Om du vill ändra kvarhållningen går du till sidan **Användning och uppskattade kostnader** från sidan Application Insights och väljer alternativet **Datakvarhållning:**

![Justera det dagliga volymlocket för telemetri](./media/pricing/pricing-005.png)

Kvarhållningen kan också [ställas in programatiskt med hjälp av PowerShell](powershell.md#set-the-data-retention) med hjälp av parametern. `retentionInDays` Om du ställer in datalagringen till 30 dagar kan du dessutom `immediatePurgeDataOn30Days` utlösa en omedelbar rensning av äldre data med parametern, vilket kan vara användbart för efterlevnadsrelaterade scenarier. Den här rensningsfunktionen exponeras endast via Azure Resource Manager och bör användas med stor försiktighet. Den dagliga återställningstiden för datavolymtaket kan konfigureras med `dailyQuotaResetTime` Azure Resource Manager för att ange parametern.

## <a name="data-transfer-charges-using-application-insights"></a>Dataöverföringsavgifter med application insights

Om du skickar data till Application Insights kan det medföra avgifter för databandbredd. Som beskrivs i [prissidan för Azure Bandwidth](https://azure.microsoft.com/pricing/details/bandwidth/), dataöverföring mellan Azure-tjänster som finns i två regioner som debiteras som utgående dataöverföring med normal hastighet. Inkommande dataöverföring är gratis. Denna avgift är dock mycket liten (få %) jämfört med kostnaderna för Application Insights logga datainmatning. Därför måste du kontrollera kostnaderna för Log Analytics fokusera på din intjesterade datavolym, och vi har vägledning för att förstå det [här](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Sammanfattning av gränser

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Inaktivera e-postmeddelanden med dagligt tak

Om du vill inaktivera den dagliga e-postmeddelandena med volymtak väljer du **Dagligt tak**under avsnittet **Konfigurera** i application insights-resursen **Usage and estimated costs** . Det finns inställningar för att skicka e-post när locket nås, samt när en justerbar varningsnivå har uppnåtts. Om du vill inaktivera alla dagliga cap volymrelaterade e-postmeddelanden avmarkera båda rutorna.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Äldre prisnivå för företag (per nod)

För tidiga användare av Azure Application Insights finns det fortfarande två möjliga prisnivåer: Basic och Enterprise. Den grundläggande prisnivån är densamma som beskrivs ovan och är standardnivån. Den innehåller alla funktioner på Enterprise-nivå utan extra kostnad. Basnivåfaktursen gäller främst volymen av data som förtärs.

> [!NOTE]
> Dessa äldre prisnivåer har bytt namn. Prisnivån Företag kallas nu **per nod** och den grundläggande prisnivån kallas nu **per GB**. Dessa nya namn används nedan och i Azure-portalen.  

Nivån Per Node (tidigare Enterprise) har en avgift per nod och varje nod får ett dagdatatillägg. På prisnivån Per Nod debiteras du för data som intas ovanför den inkluderade ersättningen. Om du använder Operations Management Suite bör du välja nivån Per Node.

För aktuella priser i din valuta och region, se [Application Insights prissättning](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> I april 2018 [introducerade](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) vi en ny prismodell för Azure-övervakning. Denna modell antar en enkel "pay-as-you-go" modell över hela portföljen av övervakningstjänster. Läs mer om den [nya prismodellen,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)hur du [bedömer effekten av att flytta till den här modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) baserat på dina användningsmönster och hur du väljer den nya [modellen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Prenumerationsberättigande per nodnivå och Operations Management Suite-prenumerationsrättigheter

Kunder som köper Operations Management Suite E1 och E2 kan få Application Insights Per Node som en extra komponent utan extra kostnad som [tidigare meddelats.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) Varje enhet i Operations Management Suite E1 och E2 innehåller en rättighet till en nod på nivån Application Insights Per Node. Varje Application Insights-nod innehåller upp till 200 MB data som intas per dag (separat från Log Analytics-datainmatning), med 90 dagars datalagring utan extra kostnad. Nivån beskrivs i mer detaljerad senare i artikeln.

Eftersom den här nivån endast gäller för kunder med en Operations Management Suite-prenumeration ser kunder som inte har en Operations Management Suite-prenumeration ett alternativ för att välja den här nivån.

> [!NOTE]
> För att säkerställa att du får det här berättigandet måste dina Application Insights-resurser finnas på prisnivån Per Node. Detta berättigande gäller endast som noder. Application Insights-resurser på per GB-nivå inser ingen nytta. Det här berättigandet visas inte i de uppskattade kostnader som visas i fönstret **Användning och uppskattad kostnad.** Om du flyttar en prenumeration till den nya Azure-övervakningsprismodellen i april 2018 är nivån per GB den enda tillgängliga nivån. Det är inte tillrådligt att flytta en prenumeration till den nya azure-övervakningsprismodellen om du har en Operations Management Suite-prenumeration.

### <a name="how-the-per-node-tier-works"></a>Så här fungerar nivån Per nod

* Du betalar för varje nod som skickar telemetri för alla appar på nivån Per nod.
  * En *nod* är en fysisk eller virtuell serverdator eller en rollinstans som är värd för din app.
  * Utvecklingsdatorer, klientwebbläsare och mobila enheter räknas inte som noder.
  * Om appen har flera komponenter som skickar telemetri, till exempel en webbtjänst och en backend-arbetare, räknas komponenterna separat.
  * [Live Metrics Stream-data](../../azure-monitor/app/live-stream.md) räknas inte i prissättningssyfte. I en prenumeration är dina avgifter per nod, inte per app. Om du har fem noder som skickar telemetri för 12 appar är debiteringen för fem noder.
* Även om avgifter anges per månad debiteras du bara för en timme då en nod skickar telemetri från en app. Timavgiften är den angivna månadsavgiften dividerat med 744 (antalet timmar i en 31-dagarsmånad).
* En datavolymallokering på 200 MB per dag ges för varje nod som identifieras (med timgranularitet). Outnyttjad dataallokering överförs inte från en dag till nästa.
  * Om du väljer prisnivån per nod får varje prenumeration ett dagligt intag av data baserat på antalet noder som skickar telemetri till application insights-resurserna i prenumerationen. Så om du har fem noder som skickar data hela dagen, har du en poolad ersättning på 1 GB tillämpas på alla Application Insights-resurser i den prenumerationen. Det spelar ingen roll om vissa noder skickar mer data än andra noder eftersom de inkluderade data delas mellan alla noder. Om application insights-resurserna får mer data än vad som ingår i den dagliga dataallokeringen för den här prenumerationen, tillkommer avgifterna för överanvändningsdata per GB. 
  * Dagdatatillägget beräknas som antalet timmar på dagen (med UTC) som varje nod skickar telemetri dividerat med 24 multiplicerat med 200 MB. Så, om du har fyra noder som skickar telemetri under 15 av de 24 timmarna på dagen, skulle de inkluderade data för den dagen vara ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Till priset av 2,30 USD per GB för dataöverprisning, skulle avgiften vara 1,15 USD om noderna skickar 1 GB data den dagen.
  * Dagtraktamentet per nod delas inte med program som du har valt nivån Per GB för. Outnyttjad ersättning överförs inte från dag till dag.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exempel på hur du bestämmer antalet distinkta nod

| Scenario                               | Totalt antal dagliga nodar |
|:---------------------------------------|:----------------:|
| 1 program med 3 Azure App Service-instanser och 1 virtuell server | 4 |
| 3 program som körs på 2 virtuella datorer; Application Insights-resurserna för dessa program finns i samma prenumeration och på nivån Per Node | 2 | 
| 4 program vars Applications Insights-resurser har samma prenumeration. varje program som kör 2 instanser under 16 lågtrafiktimmar och 4 instanser under 8 rusningstid | 13.33 |
| Molntjänster med 1 arbetarroll och 1 webbroll, var och en kör 2 instanser | 4 | 
| Ett Azure Service Fabric-kluster med 5 nod som kör 50 mikrotjänster. varje mikrotjänst som kör 3 instanser | 5|

* Den exakta nodräkningen beror på vilken Application Insights SDK ditt program använder. 
  * I SDK-versioner 2.2 och senare rapporterar både Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) och [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) varje programvärd som en nod. Exempel är datornamnet för fysiska server- och VM-värdar eller instansnamnet för molntjänster.  Det enda undantaget är ett program som bara använder [.NET Core](https://dotnet.github.io/) och Application Insights Core SDK. I så fall rapporteras bara en nod för alla värdar eftersom värdnamnet inte är tillgängligt.
  * För tidigare versioner av SDK fungerar [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) som de nyare SDK-versionerna, men [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporterar bara en nod, oavsett antalet programvärdar.
  * Om ditt program använder SDK för att ange **rollInstance** till ett anpassat värde används som standard samma värde för att fastställa antalet noder.
  * Om du använder en ny SDK-version med en app som körs från klientdatorer eller mobila enheter kan antalet nodvärden returnera ett stort antal (på grund av det stora antalet klientdatorer eller mobila enheter).

## <a name="automation"></a>Automation

Du kan skriva ett skript för att ange prisnivån med hjälp av Azure Resource Management. [Lär dig mer](powershell.md#price).

## <a name="next-steps"></a>Nästa steg

* [Samling](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
