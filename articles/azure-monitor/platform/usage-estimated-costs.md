---
title: Övervaka användning och uppskattade kostnader i Azure Monitor
description: Översikt över processen med att använda Azure Monitor-användning och sidan uppskattade kostnader
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658823"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Övervaka användning och uppskattade kostnader i Azure Monitor

> [!NOTE]
> I den här artikeln beskrivs hur du visar användning och uppskattade kostnader för flera Azure-övervakningsfunktioner. Relaterade artiklar för specifika komponenter i Azure Monitor är:
> - [Hantera användning och kostnader med Azure Monitor Logs](manage-cost-storage.md) beskriver hur du kan kontrollera dina kostnader genom att ändra din datalagringsperiod och hur du analyserar och varnar för din dataanvändning.
> - [Hantera användning och kostnader för Application Insights](../../azure-monitor/app/pricing.md) beskriver hur du analyserar dataanvändning i Application Insights.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor-prismodell

Den grundläggande Azure Monitor-faktureringsmodellen är en molnvänlig, förbrukningsbaserad prissättning ("Betala per användning"). Betala endast för det du använder. Prisinformation är tillgänglig för [aviseringar, mått, meddelanden,](https://azure.microsoft.com/pricing/details/monitor/) [Logganalys](https://azure.microsoft.com/pricing/details/log-analytics/) och [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Förutom pay-as-you-go-modellen för loggdata har Log Analytics kapacitetsreservationer, vilket gör att du kan spara så mycket som 25% jämfört med priset för betala-för-du-gå. Med prissättningen för kapacitetsreservation kan du köpa en bokning som börjar på 100 GB/dag. All användning över bokningsnivån kommer att debiteras till priset betala per användning. [Läs mer](https://azure.microsoft.com/pricing/details/monitor/) om priser för kapacitetsbokning.

Vissa kunder har åtkomst till [äldre Log Analytics-prisnivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) och den [äldre prisnivån för Enterprise Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Förstå dina Azure Monitor-kostnader

Det finns två faser för att förstå kostnaderna. Den första är när du överväger Azure Monitor som din övervakningslösning. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Uppskatta kostnaderna för att hantera din miljö

Om du ännu inte använder Azure Monitor Logs kan du använda [Azure Monitor-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att uppskatta kostnaden för att använda Azure Monitor. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor-panelen. Bläddra nedåt på sidan till Azure Monitor och välj ett av alternativen i listrutan Typ:

- Statistikfrågor och aviseringar  
- Log Analytics
- Application Insights

I var och en av dessa, prissättning kalkylatorn hjälper dig att uppskatta dina sannolika kostnader baserat på din förväntade utnyttjande.

Med Logganalys kan du till exempel ange antalet virtuella datorer och GB data som du förväntar dig att samla in från varje virtuell dator. Vanligtvis intas 1 GB till 3 GB datamånad från en vanlig Virtuell Azure-dator. Om du redan utvärderar Azure Monitor Logs kan du använda din datastatistik från din egen miljö. Se nedan för hur du bestämmer [antalet övervakade virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) och mängden data som [arbetsytan intagar](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).

På samma sätt för Application Insights, om du aktiverar funktionen "Uppskatta datavolym baserat på programaktivitet", kan du ange indata om ditt program (förfrågningar per månad och sidvisningar per månad, om du samlar in telemetri på klientsidan). och sedan räknaren kommer att berätta medianen och 90: e percentilen mängden data som samlas in av liknande applikationer. Dessa program spänner över utbudet av Application Insights-konfiguration (t.ex. vissa har standardprovtagning, vissa har ingen sampling etc.), så du har fortfarande kontroll för att minska mängden data som du intar långt under mediannivån med hjälp av sampling. Men detta är en utgångspunkt för att förstå vad andra, liknande kunder ser. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) om att uppskatta kostnader för Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Förstå din användning och uppskattade kostnader

Det är viktigt att förstå och spåra din användning en gång med Azure Monitor, och det finns omfattande uppsättning verktyg för att underlätta detta. 

Azure tillhandahåller en hel del användbara funktioner i [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hub. När du har öppnat azure **cost management + faktureringsnav** klickar du på **Kostnadshantering** och väljer [omfattning](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (uppsättning resurser att undersöka). 

Om du sedan vill visa Azure Monitor-kostnaderna för de senaste 30 dagarna klickar du på panelen **Dagliga kostnader,** väljer "Senaste 30 dagarna" under Relativa datum och lägger till ett filter som väljer tjänstnamn:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insikter och analys

Detta resulterar i en vy som:

![Skärmbild av Azure Cost Management](./media/usage-estimated-costs/010.png)

Härifrån kan du gå in från den här ackumulerade kostnadssammanfattningen för att få de finare detaljerna i vyn Kostnad för resurs. I de aktuella prisnivåerna debiteras Azure Log-data på samma uppsättning mätare oavsett om de kommer från Log Analytics eller Application Insights. Om du vill skilja kostnader från användningen Logganalys eller Application Insights kan du lägga till ett filter på **resurstyp**. Om du vill visa alla kostnader för programstatistik filtrerar du resurstypen till "microsoft.insights/components" och filtrerar Resurstyp till "microsoft.operationalinsights/workspaces". 

Mer information om din användning är tillgänglig genom [att ladda ner din användning från Azure-portalen](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). I det hämtade kalkylbladet kan du se användning per Azure-resurs per dag. I det här Excel-kalkylbladet kan användning från dina Application Insights-resurser hittas genom att först filtrera i kolumnen "Mätarkategori" för att visa "Application Insights" och "Log Analytics", och sedan lägga till ett filter i kolumnen "Instans-ID" som "innehåller microsoft.insights/components".  De flesta application insights-användning rapporteras på mätare med mätarkategorin Log Analytics, eftersom det finns en enda loggar backend för alla Azure Monitor-komponenter.  Endast Application Insights-resurser på äldre prisnivåer och webbtester i flera steg rapporteras med en mätarkategori för application insights.  Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "Enhet".  Mer information finns tillgänglig för att hjälpa dig att [förstå din Microsoft Azure-faktura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

> [!NOTE]
> Att använda **kostnadshantering** i **Azure Cost Management + Billing** hub är den bästa metoden för att i stort sett förstå övervakningskostnader.  **Ering och uppskattade kostnader** för [Logganalys](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) och [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) ger djupare insikter för var och en av dessa delar av Azure Monitor.

Ett annat alternativ för att visa din Azure Monitor-användning är sidan **Användning och uppskattade kostnader** i monitorhubben. Detta visar användningen av grundläggande övervakningsfunktioner som [aviseringar, mått, meddelanden,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)och Azure Application [Insights](https://azure.microsoft.com/pricing/details/application-insights/). För kunder i de prisplaner som är tillgängliga före april 2018 inkluderar detta även användning av Logganalys som köpts via Insights and Analytics-erbjudandet.

På den här sidan kan användarna visa sin resursanvändning för de senaste 31 dagarna, aggregerade per prenumeration. `Drill-ins`visar användningstrender under 31-dagarsperioden. En hel del data måste samlas för denna uppskattning, så ha tålamod när sidan laddas.

I det här exemplet visas övervakningsanvändning och en uppskattning av de resulterande kostnaderna:

![Skärmbild av användnings- och uppskattade kostnader](./media/usage-estimated-costs/001.png)

Välj länken i kolumnen månadsanvändning om du vill öppna ett diagram som visar användningstrender under den senaste 31-dagarsperioden: 

![Inkluderad skärmbild av stapeldiagram per nod](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Prenumerationsrättigheter för Operations Management Suite

Kunder som har köpt Microsoft Operations Management Suite E1 och E2 är berättigade till datainmatningsrättigheter per nod för [Logganalys-](https://www.microsoft.com/cloud-platform/operations-management-suite) och [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Så här får du dessa berättiganden för log analytics-arbetsytor eller application insights-resurser i en viss prenumeration: 

- Log Analytics-arbetsytor bör använda prisnivån "Per node (OMS)".
- Application Insights-resurser bör använda prisnivån "Enterprise".

Beroende på antalet noder i sviten som din organisation har köpt kan det vara fördelaktigt att flytta vissa prenumerationer till en prisnivå för användningsbaserad betalning (Per GB), men det kräver noggrant övervägande.

> [!WARNING]
> Om din organisation har nuvarande Microsoft Operations Management Suite E1 och E2 är det vanligtvis bäst att behålla dina Log Analytics-arbetsytor på prisnivån "Per node (OMS) och dina Application Insights-resurser på prisnivån "Företag". 
>
