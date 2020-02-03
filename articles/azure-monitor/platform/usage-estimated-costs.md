---
title: Övervaka användning och uppskattade kostnader i Azure Monitor
description: Översikt över processen med att använda Azure Monitor-användning och uppskattade kostnader
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 3fb51a9dc3f607d89934f6962588195e0f5c83f5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715783"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Övervaka användning och uppskattade kostnader i Azure Monitor

> [!NOTE]
> Den här artikeln beskriver hur du visar användning och uppskattade kostnader för flera Azure-övervakningsfunktioner. Relaterade artiklar för vissa komponenter i Azure Monitor är:
> - [Hantera användning och kostnader med Azure Monitor loggar](manage-cost-storage.md) beskriver hur du styr dina kostnader genom att ändra din data lagrings period och hur du analyserar och varnar för din data användning.
> - [Hantera användning och kostnader för Application Insights](../../azure-monitor/app/pricing.md) beskriver hur du analyserar data användningen i Application Insights.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor pris modell

Den grundläggande Azure Monitor fakturerings modellen är en molnbaserad, konsumtions-baserad prissättning ("betala per användning"). Betala endast för det du använder. Pris information är tillgänglig för [aviseringar, statistik, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) och [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Förutom modellen betala per användning för loggdata, har Log Analytics kapacitets reservationer, vilket gör att du kan spara så mycket som 25% jämfört med priset för betala per användning. Med kapacitets reservations priset kan du köpa en reservation som börjar på 100 GB/dag. All användning ovanför reservations nivån debiteras enligt priset för betala per användning. [Läs mer](https://azure.microsoft.com/pricing/details/monitor/) om priser för kapacitets reservationer.

Vissa kunder kommer att ha åtkomst till [äldre Log Analytics pris nivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) och den [äldre företags Application Insights pris nivån](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>Förstå Azure Monitor kostnader

Det finns två faser för att förstå kostnaderna. Det första är när du överväger Azure Monitor som din övervaknings lösning. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Beräkna kostnaderna för att hantera din miljö

Om du inte använder Azure Monitor loggar än kan du använda [pris Kalkylatorn för Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) för att beräkna kostnaden för att använda Azure Monitor. Börja med att ange "Azure Monitor" i sökrutan och klicka på den resulterande Azure Monitor panelen. Rulla ned sidan till Azure Monitor och välj ett av alternativen i list rutan typ:

- Mått frågor och aviseringar  
- Log Analytics
- Application Insights

I vart och ett av dessa kan pris kalkylatorn hjälpa dig att beräkna dina sannolika kostnader baserat på förväntad användning.

Med Log Analytics kan du till exempel ange antalet virtuella datorer och de GB data GB som du förväntar dig att samla in från varje virtuell dator. Normalt används 1 GB till 3 GB data månad från en typisk virtuell Azure-dator. Om du redan utvärderar Azure Monitor loggar redan kan du använda din data statistik från din egen miljö. Nedan finns information om hur du fastställer [antalet övervakade virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) och [mängden data som din arbets yta](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)tar med.

Om du aktiverar funktionen beräkna data volym utifrån program aktivitet på samma sätt som Application Insights, kan du ange indata om ditt program (förfrågningar per månad och sid visningar per månad, om du vill samla in telemetri på klient sidan). sedan visar kalkylatorn den median och nittionde percentils mängd data som samlas in av liknande program. Dessa program sträcker sig över ett Application Insights konfigurations intervall (t. ex. att vissa har standard sampling, har ingen sampling osv.), så du har fortfarande kontrollen att minska mängden data som du matar in långt under median nivån med hjälp av sampling. Men det här är en utgångs punkt för att förstå vad andra, liknande kunder ser. [Läs mer](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) om hur du uppskattar kostnader för Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Förstå din användning och beräknade kostnader

Det är viktigt att förstå och spåra din användning när du använder Azure Monitor, och det finns många verktyg för att under lätta detta. 

Azure ger en fantastisk mängd användbara funktioner i [Azure Cost Management + fakturerings](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hubben. När du har öppnat **Azure Cost Management + fakturerings** hubb klickar du på **Cost Management** och väljer [omfånget](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (den uppsättning resurser som ska undersökas). 

Om du sedan vill se de Azure Monitor kostnaderna under de senaste 30 dagarna klickar du på panelen **dagliga kostnader** , väljer de senaste 30 dagarna under relativa datum och lägger till ett filter som väljer tjänst namn:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insikter och analys

Detta resulterar i en vy, till exempel:

![Azure Cost Management skärm bild](./media/usage-estimated-costs/010.png)

Härifrån kan du gå vidare från denna ackumulerade kostnads Sammanfattning för att få mer information i vyn "kostnad per resurs". I de aktuella pris nivåerna debiteras Azure loggdata på samma uppsättning mätare, oavsett om den härstammar från Log Analytics eller Application Insights. Du kan lägga till ett filter för **resurs typen**om du vill avgränsa kostnaderna från Log Analytics eller Application Insights användning. Om du vill se alla Application Insights kostnader filtrerar du resurs typen till "Microsoft. Insights/Components" och för Log Analytics kostnader, filtrera resurs typ till "Microsoft. operationalinsights/arbetsyte". 

Du kan få mer information om din användning genom [att ladda ned din användning från Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). I det hämtade kalkyl bladet kan du se användning per Azure-resurs per dag. I det här Excel-kalkylbladet kan du hitta användning från dina Application Insights-resurser genom att först filtrera fram kolumnen "mätnings kategori" för att Visa "Application Insights" och "Log Analytics" och sedan lägga till ett filter i kolumnen "instance ID" som innehåller Microsoft. Insights/komponenter.  De flesta Application Insights användningen rapporteras för mätare med Log Analyticss mätar kategori, eftersom det finns en enda loggar Server del för alla Azure Monitor-komponenter.  Endast Application Insights-resurser på äldre pris nivåer och webbtester med flera steg rapporteras med en mätnings kategori av Application Insights.  Användningen visas i kolumnen "Förbrukat antal" och enheten för varje post visas i kolumnen "enhets mått".  Mer information finns för att hjälpa dig att [förstå din Microsoft Azure faktura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

> [!NOTE]
> Att använda **Cost Management** i **Azure Cost Management + fakturerings** hubb är det bästa sättet att förstå övervaknings kostnaderna.  **Användnings-och uppskattade kostnader** för [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) och [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) ger djupare insikter för var och en av dessa delar av Azure Monitor.

Ett annat alternativ för att visa din Azure Monitor användning är sidan **användning och uppskattade kostnader** i Monitor Hub. Detta visar användningen av grundläggande övervakningsfunktioner, till exempel [aviseringar, statistik, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), Azure- [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)och [Azure Application insikter](https://azure.microsoft.com/pricing/details/application-insights/). För kunder på priser avtal innan April 2018, omfattar detta även logganalys-användning som köpts via insikterna och Analytics erbjuder.

På den här sidan kan användare visa sina resursanvändningen för de senaste 31 dagarna, aggregerade per prenumeration. `Drill-ins` Visa användnings trender under den 31-dagars perioden. Stora mängder data behöver samlas för den här beräkningen så ha tålamod när sidan läses in.

Det här exemplet visar övervakad användning och en uppskattning av de resulterande kostnaderna:

![Användning och uppskattade kostnader portal skärmbild](./media/usage-estimated-costs/001.png)

Välj länken i kolumnen månatlig användning för att öppna ett diagram som visar trender för användning under de senaste 31 dagarna: 

![Ingår per nod liggande diagram skärmbild](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite-prenumerations rättigheter

Kunder som köpte Microsoft Operations Management Suite E1 och E2 är berättigade till data inmatnings rättigheter per nod för [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) och [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Att ta emot dessa rättigheter för Log Analytics-arbetsytor och Application Insights-resurser i en viss prenumeration: 

- Log Analytics-arbetsytor bör använda den ”Per nod (OMS)” prisnivå.
- Application Insights resurser bör använda pris nivån "Enterprise".

Beroende på antalet noder i serien som din organisation har köpt, kan det vara en fördel att flytta några prenumerationer till pris nivån betala per användning (per GB), men det kräver noggrant övervägande.

> [!WARNING]
> Om din organisation har nuvarande Microsoft Operations Management Suite E1 och E2, är det oftast bäst att hålla dina Log Analytics arbets ytor i pris nivån "per nod (OMS)" per nod (OMS) och dina Application Insights resurser på pris nivån "Enterprise". 
>
