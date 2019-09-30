---
title: Enhetlig avisering & övervakning i Azure Monitor ersätter klassisk avisering & övervakning
description: Översikt över pensionering av klassiska övervaknings tjänster och funktioner, som tidigare visas i Azure Portal under aviseringar (klassisk). Klassisk avisering & övervakning innehåller klassiska mått varningar för Azure-resurser, klassiska mått varningar för Application Insights, klassiska webtest-aviseringar för Application Insights, klassiska anpassade Metric-baserade aviseringar för Application Insights och klassisk aviseringar för Application Insights SmartDetection v1
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: ea50a4f10aa1d794f875cb1eb9601774bec146cd
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676898"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Enhetlig avisering & övervakning i Azure Monitor ersätter klassisk avisering & övervakning

Azure Monitor har nu blivit en enhetlig tjänst för fullständig stack övervakning som nu stöder "ett mått" och "en avisering" mellan resurser. Mer information finns i vårt [blogg inlägg på nya Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nya plattformarna för Azure-övervakning och-avisering har utformats för att vara snabbare, smartare och utöknings Bar – hålla jämna steg med den växande expanse av molnbaserad data behandling och online med Microsoft Intelligent Cloud-filosofi. 

Med den nya plattformen för Azure-övervakning och avisering kommer vi att ta bort den "klassiska" övervaknings-och varnings plattformen som finns i avsnittet *klassiska aviseringar* i Azure-aviseringar, **kommer att föråldras med 2019 i Azures offentliga moln** . [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) och [Azure Kina 21Vianet](https://docs.azure.cn/) kommer inte att påverkas.

> [!NOTE]
> På grund av fördröjning i uppsamlingen av migreringen har den senaste indragnings tiden för migrering av klassisk avisering utökats [till 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) från det ursprungligen presenterade datumet den 30 juni 2019.

 ![Klassisk avisering i Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Vi rekommenderar att du kommer igång och återskapar dina aviseringar på den nya plattformen. För kunder som har ett stort antal aviseringar, kommer vi att [lanseras i faser](alerts-understand-migration.md#rollout-phases), ett särskilt [Migreringsverktyg](alerts-using-migration-tool.md) för att flytta befintliga klassiska varningar till det nya aviserings systemet utan avbrott eller ytterligare kostnader.

> [!IMPORTANT]
> Klassiska varnings regler som skapats i aktivitets loggen är inte föråldrade eller migreras inte. Alla klassiska varnings regler som skapats i aktivitets loggen kan nås och används som de är från nya Azure Monitor-aviseringar. Mer information finns i [skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). På samma sätt kan aviseringar på Service Health kommas åt och användas som de är från avsnittet ny Service Health. Mer information finns i [aviseringar om meddelanden om tjänst hälsa](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Enhetliga mått och aviseringar i Application Insights

Azure Monitors nya mått plattform kommer nu att komma från Application Insights. Den här flytten innebär att Application Insights kopplar till åtgärds grupper, vilket ger mycket fler alternativ än bara de tidigare e-post-och webhook-anropen. Aviseringar kan nu utlösa röst samtal, Azure Functions, Logic Apps, SMS och ITSM-verktyg som ServiceNow och Automation-runbooks. Med den nya plattformen för övervakning och avisering i nära real tid kan Application Insights användare använda samma teknik för att övervaka andra Azure-resurser och underfästa övervakning av Microsoft-produkter.

Den nya enhetliga övervakningen och aviseringen för Application Insights kommer att omfatta:

- **Application Insights plattforms mått** – som ger populära förbyggda mått från Application Insights produkt. Mer information finns i den här artikeln om hur du använder [plattforms mått för Application Insights på nya Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights tillgänglighet och webbtest** – vilket ger dig möjlighet att utvärdera svars tider och tillgänglighet för din webbapp eller server. Mer information finns i den här artikeln om [att använda tillgänglighets test och aviseringar för Application Insights på nya Azure Monitor](../../azure-monitor/app/monitor-web-app-availability.md).
- **Application Insights anpassade mått** – som gör att du kan definiera och generera egna mått för övervakning och aviseringar. Mer information finns i den här artikeln om hur du använder [anpassad mått för Application Insights på nya Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights fel avvikelser (del av Smart identifiering)** – som automatiskt meddelar dig i nära real tid om din webbapp upplever en onormal ökning av antalet misslyckade HTTP-förfrågningar eller beroende anrop. Mer information finns i den här artikeln om att använda [Smart identifiering – fel avvikelser](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Enhetliga mått och aviseringar för andra Azure-resurser

Sedan mars 2018 har nästa generations avisering och flerdimensionell övervakning för Azure-resurser varit i tillgänglighet. Nu är den nya mått plattformen och aviseringar snabbare med funktioner i nästan real tid. Det är viktigt att de nyare mått plattforms aviseringarna ger mer detaljerad information, eftersom den nya plattformen innehåller alternativ för dimensioner, vilket gör att du kan segmentera och filtrera efter en speciell värde kombination, villkor eller åtgärd. Precis som med alla aviseringar i nya Azure Monitor, är de nyare mått aviseringarna mer utöknings bara med användningen av ActionGroups – vilket gör att meddelanden kan utökas utöver e-post eller webhook till SMS, röst, Azure Function, Automation Runbook med mera. Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
Nya mått för Azure-resurser är tillgängliga som:

- **Azure Monitor standard plattforms mått** – som ger populära förifyllda mått från olika Azure-tjänster och-produkter. Mer information finns i den här artikeln om [mått som stöds på Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) och [stöd för mått varningar på Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor anpassade mått** – som tillhandahåller mått från användar drivna källor, inklusive Azure-diagnostik-agenten. Mer information finns i den här artikeln om [anpassade mått i Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Med anpassade mått kan du också publicera mått som samlats in av [Windows Azure-diagnostik-agenten](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) och [InfluxDatain ympkvistar-agenten](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Dra tillbaka klassisk övervaknings-och varnings plattform

Som tidigare nämnts kan den klassiska övervaknings-och varnings plattformen som för närvarande är användbar från [avsnittet om aviseringar (klassisk)](../../azure-monitor/platform/alerts-classic.overview.md) i Azure Portal dras tillbaka under de kommande månaderna, förutsatt att de har ersatts av det nyare systemet.
Äldre klassisk övervakning och avisering kommer att dras tillbaka den 31 augusti 2019; inklusive stängning av relaterade API: er, Azure Portal gränssnitt och tjänster i det. Mer specifikt är dessa funktioner inaktuella:

- Äldre (klassiska) mått och aviseringar för Azure-resurser som för närvarande är tillgängliga via [aviseringar (klassisk)](../../azure-monitor/platform/alerts-classic.overview.md) i Azure Portal; tillgänglig som [Microsoft. Insights/alertrules-](https://docs.microsoft.com/rest/api/monitor/alertrules) resurs
- Äldre (klassisk) plattform och anpassade mått för Application Insights samt avisering om dem som för närvarande är tillgängliga via [aviseringar (klassisk)](../../azure-monitor/platform/alerts-classic.overview.md) i Azure Portal och som är tillgängliga som [Microsoft. Insights/alertrules-](https://docs.microsoft.com/rest/api/monitor/alertrules) resurs
- Varning om äldre (klassisk) fel avvikelser är för närvarande tillgängliga som [Smart identifiering i Application Insights](../../azure-monitor/app/proactive-diagnostics.md) i Azure Portal; med aviseringar som kon figurer ATS i [avsnittet aviseringar (klassisk)](../../azure-monitor/platform/alerts-classic.overview.md) i Azure Portal

Alla klassiska övervaknings-och aviserings system, inklusive motsvarande [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [Azure Portal sida](../../azure-monitor/platform/alerts-classic-portal.md)och [resurs mal len](../../azure-monitor/platform/alerts-enable-template.md) kommer att vara användbara tills slutet av augusti 2019. 

I slutet av augusti 2019 i Azure Monitor:

- Den klassiska övervaknings-och aviserings tjänsten dras tillbaka och kan inte längre användas för att skapa nya varnings regler.
- Eventuella varnings regler som fortsätter att finnas i aviseringar (klassisk) utöver augusti 2019 fortsätter att köras och utlösa aviseringar, men är inte tillgängliga för ändring.
- Från och med den 2019 september flyttas aviserings regler i klassisk övervakning & aviseringar som kan migreras automatiskt av Microsoft till motsvarande i den nya Azure Monitor-plattformen i faser som sträcker sig över några veckor. Processen kommer att vara sömlös utan drift avbrott och kunder har ingen förlust av övervaknings täckning.
- Aviserings regler som har flyttats till den nya aviserings plattformen tillhandahåller övervaknings täckning som tidigare men kommer att utlösa aviseringar med nya nytto laster. Alla e-postadresser, webhook-slutpunkter eller Logic app-länkar som är kopplade till den klassiska varnings regeln överförs vid migrering, men kanske inte fungerar korrekt eftersom aviserings nytto lasten är annorlunda i den nya plattformen.
- Vissa [klassiska varnings regler som inte kan migreras automatiskt](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) och kräver manuell åtgärd från användarna fortsätter att köras fram till 2020 juni.

> [!IMPORTANT]
> Microsoft Azure övervakare har distribuerats i faser- [verktyget för att frivilligt migrera](alerts-using-migration-tool.md) sina klassiska varnings regler till den nya plattformen snart. Och kör det i kraft för alla klassiska varnings regler som fortfarande finns och som kan migreras, från och med 2019 september. Kunderna måste se till att den klassiska varnings regel nytto lasten är anpassad för att hantera den nya nytto lasten från [enhetliga mått och aviseringar i Application Insights](#unified-metrics-and-alerts-in-application-insights) eller [enhetliga mått och aviseringar för andra Azure-resurser](#unified-metrics-and-alerts-for-other-azure-resources), efter migreringen av de klassiska varnings reglerna. Mer information finns i [förbereda för klassisk migrering av aviserings regel](alerts-prepare-migration.md)

Den här artikeln uppdateras kontinuerligt med länkar & information om de nya funktionerna för Azure Monitoring-& avisering, samt tillgänglighet för verktyg för att hjälpa användarna att införa den nya Azure Monitor-plattformen.

## <a name="pricing-for-migrated-alert-rules"></a>Priser för migrerade varnings regler

Vi distribuerar ett Migreringsverktyg för att hjälpa dig att migrera dina Azure Monitor [klassiska aviseringar](../../azure-monitor/platform/alerts-classic.overview.md) till den nya aviserings upplevelsen. Migrerade varningsregler och motsvarande migrerade åtgärdsgrupper (e-post, webhook eller LogicApp) är kostnadsfria även i fortsättningen. De funktioner du haft med klassiska varningar, till exempel möjligheten att redigera tröskel, sammansättningstyp och detaljnivå för sammansättningen, kommer att finnas tillgängliga utan kostnad även med din migrerade varningsregel. Om du däremot redigerar den migrerade varningsregeln för att använda någon av aviseringsplattformens nya funktioner, aviseringar eller åtgärdstyper, tillkommer motsvarande avgift. Mer information om priser för varnings regler och aviseringar finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

Följande är exempel på fall där du måste betala en avgift för varningsregeln:

- Alla nya (icke-migrerade) varningsregler som skapats utanför kostnadsfria enheter på den nya Azure Monitor-plattformen
- Alla data som matats in och sparats utanför kostnadsfria enheter som omfattas av Azure Monitor
- Alla multitestbaserade webbtester som körts av Application Insights
- Alla anpassade mått som lagrats utanför de kostnadsfria enheter som ingår i Azure Monitor
- Eventuella migrerade varnings regler som redige ras för att använda nya mått, t. ex. frekvens, flera resurser/dimensioner, [dynamiska tröskelvärden](alerts-dynamic-thresholds.md), ändring av resurs/signal och så vidare.
- Alla migrerade åtgärds grupper som redige ras för att använda nyare meddelanden eller åtgärds typer som SMS, röst samtal och/eller ITSM-integrering.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [nya enhetliga Azure Monitor](../../azure-monitor/overview.md).
* Läs mer om de nya [Azure-aviseringarna](../../azure-monitor/platform/alerts-overview.md).
