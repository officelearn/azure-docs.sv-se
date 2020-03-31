---
title: Uppdatering av klassisk avisering & övervakning i Azure Monitor
description: Beskrivning av pensionering av klassiska övervakningstjänster och funktioner, som tidigare visats i Azure-portalen under Aviseringar (klassisk).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659483"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Enhetlig aviseringar & övervakning i Azure Monitor ersätter klassisk avisering & övervakning

Azure Monitor har nu blivit en enhetlig fullständig stackövervakningstjänst, som nu stöder "Ett mått" och "One Alerts" över resurser. Mer information finns i vårt [blogginlägg på nya Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nya Azure-övervaknings- och varningsplattformarna har byggts för att bli snabbare, smartare och utökningsbara – hålla jämna steg med den växande utvecklingen av molntjänster och i linje med Microsoft Intelligent Cloud-filosofin. 

Med den nya Azure-övervaknings- och aviseringsplattformen på plats kommer vi att dra tillbaka den "klassiska" övervaknings- och varningsplattformen – som finns inom *vyn klassiska aviseringar* i Azure-aviseringar , **kommer att vara inaktuella i augusti 2019 i offentliga Azure-moln**. [Azure Government-molnet](../../azure-government/documentation-government-welcome.md) och [Azure China 21Vianet](https://docs.azure.cn/) påverkas inte.

> [!NOTE]
> På grund av förseningar i utrullningen av migreringsverktyget har pensioneringsdatumet för klassisk [registreringsmigrering förlängts till den 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) från det ursprungligen meddelade datumet den 30 juni 2019.

 ![Klassisk avisering i Azure-portalen](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Vi uppmuntrar dig att komma igång och återskapa dina aviseringar i den nya plattformen. För kunder som har ett stort antal aviseringar rullar vi [ut i faser](alerts-understand-migration.md#rollout-phases), ett [frivilligt migreringsverktyg](alerts-using-migration-tool.md) för att flytta befintliga klassiska aviseringar till det nya varningssystemet utan avbrott eller extra kostnader.

> [!IMPORTANT]
> Klassiska varningsregler som skapas i aktivitetsloggen kommer inte att vara inaktuella eller migrerade. Alla klassiska varningsregler som skapas i aktivitetsloggen kan nås och användas som de är från den nya Azure Monitor - Aviseringar. Mer information finns i [Skapa, visa och hantera aktivitetsloggaviseringar med Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). På samma sätt kan aviseringar om tjänsthälsa nås och användas som de är från det nya avsnittet Service Health. Mer information finns i [aviseringar om hälsomeddelanden för tjänsten](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Enhetliga mått och aviseringar i application insights

Azure Monitor nyare mått plattform kommer nu att driva övervakning som kommer från Application Insights. Detta drag innebär Application Insights kommer att ansluta till åtgärdsgrupper, vilket ger mycket fler alternativ än bara tidigare e-post och webhook samtal. Aviseringar kan nu utlösa röstsamtal, Azure-funktioner, logic apps, SMS och ITSM-verktyg som ServiceNow och Automation Runbooks. Med övervakning och aviseringar i nära realtid gör den nya plattformen det möjligt för Application Insights-användare att utnyttja samma teknik som driver övervakning över andra Azure-resurser och som stöder övervakning av Microsoft-produkter.

Den nya enhetliga övervakningen och aviseringen för application insights omfattar:

- **Programinsiktsplattformsmått** – som ger populära fördefinierade mått från Application Insights-produkten. Mer information finns i den här artikeln om hur du använder [plattformsmått för programstatistik på ny Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Tillgänglighet för application insights och webbtest** – vilket ger dig möjlighet att bedöma webbappens eller serverns svarstider och tillgänglighet. Mer information finns i den här artikeln om hur du använder [tillgänglighetstester och aviseringar för programinsikter på ny Azure Monitor](../../azure-monitor/app/monitor-web-app-availability.md).
- **Application Insights Anpassade mått** – som låter dig definiera och avge sina egna mått för övervakning och aviseringar. Mer information finns i den här artikeln om hur du använder [anpassade mått för programstatistik på ny Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights Failure Anomalier (en del av Smart Detection)** – som automatiskt meddelar dig i nära realtid om din webbapp upplever en onormal ökning av antalet misslyckade HTTP-begäranden eller beroendeanrop. Mer information finns i den här artikeln om hur du använder [Smart Identifiering - Felavvikelser](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Enhetliga mått och aviseringar för andra Azure-resurser

Sedan mars 2018 har nästa generation av aviseringar och flerdimensionell övervakning för Azure-resurser varit i tillgänglighet. Nu är den nyare metriska plattformen och aviseringar snabbare med nästan realtidsfunktioner. Ännu viktigare är att de nyare måttplattformsaviseringarna ger mer detaljeradhet, eftersom den nyare plattformen innehåller alternativet med dimensioner, som gör att du kan segmentera och filtrera till specifik värdekombination, villkor eller åtgärd. Precis som alla aviseringar i den nya Azure Monitor är de nyare måttaviseringarna mer utökningsbara med hjälp av ActionGroups – vilket gör att meddelanden kan expandera utöver e-post eller webhook till SMS, Röst, Azure-funktion, Automation Runbook med mera. Mer information finns i [Skapa, visa och hantera måttaviseringar med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
Nyare mått för Azure-resurser är tillgängliga som:

- **Azure Monitor Standard-plattformsmått** – som ger populära förifyllda mått från olika Azure-tjänster och -produkter. Mer information finns i den här artikeln om [mått som stöds på Azure Monitor-](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) och [Support-måttaviseringar på Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor Custom metrics** – som tillhandahåller mått från användardrivna källor, inklusive Azure Diagnostics-agenten. Mer information finns i den här artikeln om [anpassade mått i Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Med hjälp av anpassade mått kan du också publicera mått som samlats in av [Windows Azure Diagnostics agent](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) och [InfluxData Telegraf agent](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Pensionering av Classic övervakning och varning plattform

Som tidigare nämnts kommer den klassiska [övervaknings- och varningsplattform](../../azure-monitor/platform/alerts-classic.overview.md) som för närvarande kan användas från avsnittet Aviseringar (klassisk) i Azure-portalen att dras tillbaka under de kommande månaderna med tanke på att de har ersatts av det nyare systemet.
Äldre klassisk övervakning och varningar kommer att dras tillbaka den 31 augusti 2019; inklusive stängningen av relaterade API:er, Azure-portalgränssnitt och tjänster i det. Specifikt kommer dessa funktioner att vara inaktuella:

- Äldre (klassiska) mått och aviseringar för Azure-resurser som för närvarande är tillgängliga via [alerter (klassisk) i](../../azure-monitor/platform/alerts-classic.overview.md) Azure Portal. tillgänglig som [microsoft.insights/alertrules-resurs](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Äldre (klassisk) plattform och anpassade mått för Application Insights samt aviseringar om dem som för närvarande är tillgängliga via [alerter (klassisk) i](../../azure-monitor/platform/alerts-classic.overview.md) Azure-portalen och tillgänglig som [microsoft.insights/alertrules-resurs](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Äldre (klassiska) felavvikelser är för närvarande tillgänglig som [smart identifiering i Application Insights](../../azure-monitor/app/proactive-diagnostics.md) i Azure-portalen. med aviseringar konfigurerade som visas i [avsnittet Aviseringar (klassisk)](../../azure-monitor/platform/alerts-classic.overview.md) i Azure Portal

Alla klassiska övervaknings- och varningssystem, inklusive motsvarande [API,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md) [Azure-portalsida](../../azure-monitor/platform/alerts-classic-portal.md)och [Resursmall,](../../azure-monitor/platform/alerts-enable-template.md) kan användas fram till slutet av augusti 2019. 

I slutet av augusti 2019, i Azure Monitor:

- Tjänsten Klassisk övervakning och aviseringar kommer att dras tillbaka och inte längre tillgänglig för att skapa nya varningsregler.
- Alla varningsregler som fortsätter att finnas i Aviseringar (klassisk) efter augusti 2019 fortsätter att köra och brandmeddelanden, men inte vara tillgängliga för ändringar.
- Från och med september 2019 flyttas varningsregler i klassisk övervakning & aviseringar som kan migreras automatiskt av Microsoft till motsvarande i den nya Azure-övervakningsplattformen i faser som sträcker sig över några veckor. Processen kommer att vara sömlös utan driftstopp och kunderna kommer inte att ha någon förlust i övervakning täckning.
- Varningsregler som migreras till den nya varningsplattformen kommer att ge övervakningstäckning som tidigare men kommer att avfyra meddelanden med nya nyttolaster. Alla e-postadresser, webhook-slutpunkter eller logikapplänk som är associerade med den klassiska varningsregeln överförs när de migreras, men kanske inte fungerar korrekt eftersom nyttolasten för aviseringar kommer att vara annorlunda i den nya plattformen.
- Vissa [klassiska varningsregler som inte kan migreras automatiskt](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) och kräver manuell åtgärd från användare fortsätter att köras fram till juni 2020.

> [!IMPORTANT]
> Microsoft Azure Monitor har rullat ut i faser [verktyg för att frivilligt migrera](alerts-using-migration-tool.md) sina klassiska varningsregler på den nya plattformen snart. Och kör det med våld för alla klassiska varningsregler som fortfarande finns och kan migreras, med början i september 2019. Kunderna måste se till att automatisering som förbrukar klassisk varningsregel nyttolast anpassas för att hantera den nya nyttolasten från [enhetliga mått och aviseringar i Application Insights](#unified-metrics-and-alerts-in-application-insights) eller [Unified Metrics and Alerts for other Azure-resurser](#unified-metrics-and-alerts-for-other-azure-resources), efter migreringen av de klassiska varningsreglerna. Mer information finns i [förbereda för klassisk aviseringsregelmigrering](alerts-prepare-migration.md)

Den här artikeln kommer att uppdateras kontinuerligt med länkar & information om den nya Azure-övervakningen & aviseringsfunktioner, samt tillgången till verktyg för att hjälpa användare att använda den nya Azure Monitor-plattformen.

## <a name="pricing-for-migrated-alert-rules"></a>Prissättning för regler för migrerad varning

Vi lanserar ett migreringsverktyg som hjälper dig att migrera dina klassiska Azure [Monitor-aviseringar](../../azure-monitor/platform/alerts-classic.overview.md) till den nya aviseringarupplevelsen. De migrerade varningsreglerna och motsvarande migrerade åtgärdsgrupper (e-post, webhook eller LogicApp) förblir kostnadsfria. De funktioner du hade med klassiska aviseringar, inklusive möjligheten att redigera tröskelvärdet, aggregeringstypen och aggregeringsgranulariteten fortsätter att vara tillgänglig gratis med den migrerade varningsregeln. Om du redigerar den migrerade varningsregeln för att använda någon av de nya varningsplattformsfunktionerna, meddelandena eller åtgärdstyperna tillkommer en motsvarande avgift. Mer information om prissättningen för varningsregler och meddelanden finns i [Azure Monitor Pricing](https://azure.microsoft.com/pricing/details/monitor/).

Följande är exempel på fall där du kommer att debiteras en avgift för din varningsregel:

- Alla nya (icke-migrerade) varningsregel som skapas utanför lediga enheter på den nya Azure Monitor-plattformen
- Alla data som förtärs och behålls utöver kostnadsfria enheter som ingår i Azure Monitor
- Alla webbtester för flera test som utförs av Application Insights
- Alla anpassade mått som lagras utöver kostnadsfria enheter som ingår i Azure Monitor
- Alla migrerade varningsregler som redigeras för att använda nyare måttaviseringsfunktioner som frekvens, flera resurser/dimensioner, [dynamiska tröskelvärden,](alerts-dynamic-thresholds.md)ändring av resurs/signal och så vidare.
- Alla migrerade åtgärdsgrupper som redigeras för att använda nyare meddelanden eller åtgärdstyper som SMS, röstsamtal och/eller ITSM-integrering.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [nya enhetliga Azure Monitor](../../azure-monitor/overview.md).
* Läs mer om de nya [Azure-aviseringarna](../../azure-monitor/platform/alerts-overview.md).
