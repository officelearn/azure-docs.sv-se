---
title: Loggbaserade och preaggregeras mått i Azure Application Insights | Microsoft Docs
description: Varför ska man använda du loggbaserade jämfört med preaggregeras mått i Azure Application Insights
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitaly.gorbenko
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d095be8c5d921001ab4a492d2385938020fa8ce0
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967222"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Loggbaserade och preaggregeras mått i Application Insights

Den här artikeln förklarar skillnaden mellan ”traditionella” Application Insights-mått som baseras på loggar och preaggregeras mått som finns i offentlig förhandsversion. Båda typerna av mått är tillgängliga för användare av Application Insights och var och en ger ett unikt värde i att övervaka programmets hälsotillstånd, diagnostik och analys. Utvecklare som instrumentering av program kan bestämma vilken typ av mått som passar bäst för ett visst scenario, beroende på storleken på programmet, förväntade mängden telemetri och företagets krav för precision för mått och aviseringar.

## <a name="log-based-metrics"></a>Loggbaserade mått

Tills nyligen har enbart programövervakning i datamodellen för telemetri i Application Insights baserat på ett litet antal fördefinierade typer av händelser, till exempel begäranden, undantag, beroendeanrop, sidvisningar, osv. Utvecklare kan använda SDK: N för att generera dessa händelser antingen manuellt (genom att skriva kod som explicit anropar SDK) eller de kan förlitar sig på Automatisk insamling av händelser från automatisk instrumentering. I båda fallen Application Insights-serverdelen lagrar alla insamlade händelser som loggar och Application Insights-blad i Azure portal som fungerar som ett analytiska och diagnostiska verktyg för att visualisera händelsebaserad data från loggar.

Använda loggar för att behålla en fullständig uppsättning händelser kan ge bra värde för analys och diagnostik. Du kan till exempel få ett exakt antal begäranden till en viss URL med antalet distinkta användare som har gjort dessa anrop. Eller du kan få detaljerad diagnostik spårning, inklusive undantag och beroendeanrop för en användarsession. Med den här typen av information kan avsevärt förbättra insyn i programhälsa och användning, så att du kan minska tiden krävs för att diagnostisera problem med en app. 

På samma gång, samla in en fullständig uppsättning händelser kan vara opraktiskt (eller till och med omöjligt) för program som genererar mycket telemetri. För situationer när mängden händelser är för hög Application Insights implementerar flera telemetri volym minskning tekniker, till exempel [sampling](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) och [filtrering](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) som minska antalet händelser som samlas in och lagras. Tyvärr sänker att antalet lagrade händelser också riktighet mått som måste utföra Frågetid aggregeringar av händelser som lagras i loggarna i bakgrunden.

> [!NOTE]
> I Application Insights kallas de mått som baseras på Frågetid sammanställning händelser och mått som lagras i loggarna loggbaserade mått. De här måtten har normalt många dimensioner i händelseegenskaperna, vilket gör dem överlägsen för analys, men det arbete du utfört de här måtten påverkas negativt av sampling och filtrering.

## <a name="pre-aggregated-metrics"></a>Preaggregeras mått

Förutom loggbaserade mått levererat med 2018, Application Insights-teamet en offentlig förhandsversion av mått som lagras i en särskild databas som är optimerad för tidsserier. De nya mått behålls inte längre som enskilda händelser med många olika egenskaper. De lagras i stället som preaggregeras tidsserier och endast med viktiga dimensioner. Detta gör den nya måtten överlägsen när en fråga körs: hämtar data händer mycket snabbare och kräver mindre beräkningskapacitet. Detta innebär möjliggör nya scenarier som [nära avisering i realtid på dimensioner mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)och mer responsiv [instrumentpaneler](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards), med mera.

> [!IMPORTANT]
> Både loggbaserade och preaggregeras mått samexistera i Application Insights. Skilja mellan två i Application Insights UX preaggregeras mått kallas ”Standard mått (förhandsgranskning)”, medan de traditionella mått från händelserna har bytt namn till ”loggbaserade mått”.

Nyare SDK: er ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK eller senare för .NET) före aggregerade mätvärden vid insamling av innan telemetri volym minskning tekniker startar. Det innebär att det arbete du utfört de nya mått inte påverkas av sampla och filtrera när du använder den senaste Application Insights SDK.

För SDK: er som inte implementerar aggregering (det vill säga äldre versioner av Application Insights SDK: er eller webbläsare instrumentation) fyller Application Insights-serverdelen fortfarande den nya måtten genom att sammanställa händelser som tagits emot av programmet Insights-insamlingsslutpunkten för händelsen. Det innebär att även om du inte dra fördel av lägre mängden data som överförs över nätverket, du kan fortfarande använda preaggregeras mått och få bättre prestanda och stöd för nästan i realtid dimensionell aviseringar med SDK: er inte stöds före aggregera mått vid insamling av.

Det är värt att nämna att samlingens slutpunkt förväg aggregerar händelser innan inmatningssampling, vilket innebär att [inmatningssampling](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) kommer aldrig inverkan korrektheten i förväg aggregerade mätvärden, oavsett vilken SDK-version du Använd med ditt program.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Med aggregering med anpassade mått för Application Insights

Du kan använda aggregering med anpassade mått. Två huvudsakliga fördelar är möjligheten att konfigurera och Avisera om en dimension av ett anpassat mått och minskar mängden data som skickas från SDK: N till insamlingsslutpunkten för Application Insights.

Det finns flera [sätt att skicka anpassade mått från Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Om din version av SDK: N erbjuder den [GetMetric och TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) metoder, detta är det bästa sättet för skicka anpassade mått, eftersom i det här fallet aggregering som händer i SDK, inte bara att minska mängden data som lagras i Azure, utan även mängden data som överförs från SDK till Application Insights. Annars kan du använda den [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metod som kommer före att aggregera mått händelser vid datainmatning.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Anpassade mått dimensioner och aggregering

Alla mått som du skickar med hjälp av [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) eller [GetMetric och TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API-anrop kan automatiskt lagras i både loggar och mått. Medan du loggbaserade-versionen av din anpassade mått förblir alltid alla dimensioner, lagras den preaggregeras versionen av måttet som standard med inga dimensioner. Du kan aktivera insamling av dimensionerna för anpassade mått på den [användning och uppskattade kostnader](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) fliken genom att markera ”Aktivera avisering för anpassade mått dimensioner”: 

![Användning och uppskattade kostnader](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Varför är samling med anpassade mått dimensioner inaktiverad som standard?

Insamling av anpassade mått dimensioner är inaktiverat som standard eftersom i framtiden lagra anpassade mått med dimensioner faktureras separat från Application Insights medan lagra nolldimensionella anpassade mått fortsätter att vara kostnadsfria (upp till en kvot) . Du kan lära dig om de kommande modellen prisändringar på vår officiella [prissättningssidan](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Skapa diagram och utforska log-baserade och standard preaggregeras mått

Använda Azure Monitor Metrics Explorer för att rita diagram från preaggregeras och loggbaserade mått och till författare instrumentpaneler med diagram. Använd väljaren för namnområdet för att växla mellan standard (förhandsversion) och loggbaserade mått när du har valt önskade Application Insights-resursen, eller välj ett anpassat mått namnområde:

![Metrisk namnområde](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Nästa steg

* [Nära avisering i realtid](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric och TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
