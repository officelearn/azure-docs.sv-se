---
title: Azure Monitor loggar med en app med flera data
description: Konfigurera och använda Azure Monitor-loggar med en Azure SQL Database SaaS-app med flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214493"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Konfigurera och använda Azure Monitor-loggar med en flerenant SQL Database SaaS-app

I den här självstudien konfigurerar och använder du [Azure Monitor-loggar](/azure/log-analytics/log-analytics-overview) för att övervaka elastiska pooler och databaser. Den här självstudien bygger på [självstudiekursen För prestandaövervakning och hantering](saas-dbpertenant-performance-monitoring.md). Den visar hur du använder Azure Monitor-loggar för att utöka övervakningen och aviseringen som finns i Azure-portalen. Azure Monitor-loggar stöder övervakning av tusentals elastiska pooler och hundratusentals databaser. Azure Monitor-loggar tillhandahåller en enda övervakningslösning som kan integrera övervakning av olika program och Azure-tjänster i flera Azure-prenumerationer.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Installera och konfigurera Azure Monitor-loggar.
> * Använd Azure Monitor-loggar för att övervaka pooler och databaser.

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip-biljetterna SaaS-appen för klient-per-klienter distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska programmet Wingtip Tickets SaaS database-per-tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Se [självstudiekursen för prestandaövervakning och hantering](saas-dbpertenant-performance-monitoring.md) för en diskussion om SaaS-scenarier och mönster och hur de påverkar kraven på en övervakningslösning.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Övervaka och hantera prestanda för databaser och elastisk pool med Azure Monitor-loggar

För Azure SQL Database är övervakning och avisering tillgänglig på databaser och pooler i Azure-portalen. Den här inbyggda övervakningen och aviseringen är praktisk, men det är också resursspecifikt. Det innebär att det är mindre väl lämpad att övervaka stora installationer eller ge en enhetlig vy över resurser och prenumerationer.

För scenarier med stora volymer kan du använda Azure Monitor-loggar för övervakning och avisering. Azure Monitor är en separat Azure-tjänst som möjliggör analys över loggar som samlats in på en arbetsyta från potentiellt många tjänster. Azure Monitor-loggar tillhandahåller ett inbyggt frågespråk och datavisualiseringsverktyg som möjliggör analys av operativa data. SQL Analytics-lösningen innehåller flera fördefinierade elastiska pool- och databasövervaknings- och aviseringsvyer och frågor. Azure Monitor-loggar ger också en anpassad vydesigner.

OMS-arbetsytor kallas nu för Log Analytics-arbetsytor. Logganalysarbetsytor och analyslösningar öppnas i Azure-portalen. Azure-portalen är den nyare åtkomstpunkten, men det kan vara vad som finns bakom Operations Management Suite-portalen i vissa områden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Skapa prestandadiagnostiska data genom att simulera en arbetsbelastning på dina klienter 

1. Öppna *.. WingtipTicketsSSaaS-MultiTenantDb-master\\Lärande\\Moduler Prestandaövervakning och hantering\\Demo-PerformanceMonitoringAndManagement.ps1 . \\* Håll det här skriptet öppet eftersom du kanske vill köra flera av inläsningsgenereringsscenarierna under den här självstudien.
1. Om du inte redan har gjort det, etablera en batch av klienter för att göra övervakningskontexten mer intressant. Den här processen tar några minuter.

   a. Ange **$DemoScenario = 1**, _Etablera en batch med klienter_.

   b. Om du vill köra skriptet och distribuera ytterligare 17 klienter trycker du på F5.

1. Starta nu lastgeneratorn för att köra en simulerad belastning på alla klienter.

    a. Ställ in **$DemoScenario = 2**, _Generera normal intensitet belastning (ca 30 DTU)_.

    b. Om du vill köra skriptet trycker du på F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskripten För Wingtip Tickets SaaS-databas per klient

Wingtip Biljetter SaaS multitenant databas skript och program källkod finns i [WingtipTicketsSSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Steg för att hämta och häva blockeringen av PowerShell-skripten för Wingtip-biljetter finns i den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installera och konfigurera Log Analytics-arbetsytan och Azure SQL Analytics-lösningen

Azure Monitor är en separat tjänst som måste konfigureras. Azure Monitor-loggar samlar in loggdata, telemetri och mått på en Log Analytics-arbetsyta. Precis som andra resurser i Azure måste en Log Analytics-arbetsyta skapas. Arbetsytan behöver inte skapas i samma resursgrupp som de program som den övervakar. Att göra det ofta är det mest meningsfulla ändå. För appen Wingtip-biljetter använder du en enskild resursgrupp för att se till att arbetsytan tas bort med programmet.

1. Öppna *.. WingtipTicketsSSaaS-MultiTenantDb-master\\Utbildningsmoduler\\Prestandaövervakning och\\hantering Logg Analytics\\Demo-LogAnalytics.ps1 . \\*
1. Om du vill köra skriptet trycker du på F5.

Nu kan du öppna Azure Monitor-loggar i Azure-portalen. Det tar några minuter att samla in telemetri på log analytics-arbetsytan och göra den synlig. Ju längre du lämnar systemet samla in diagnostiska data, desto mer intressant upplevelsen är. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Använda Log Analytics-arbetsytan och SQL Analytics-lösningen för att övervaka pooler och databaser


I den här övningen öppnar du Log Analytics-arbetsytan i Azure-portalen för att titta på den telemetri som samlats in för databaser och pooler.

1. Bläddra till [Azure-portalen](https://portal.azure.com). Välj **Alla tjänster** för att öppna Log Analytics-arbetsytan. Sök sedan efter Logganalys.

   ![Öppna arbetsytan Logganalys](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Välj arbetsytan _wtploganalytics-&lt;&gt;user_.

1. Välj **Översikt om** du vill öppna logganalyslösningen i Azure-portalen.

   ![Översikt](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Det kan ta ett par minuter innan lösningen är aktiv. 

1. Välj **panelen Azure SQL Analytics** för att öppna den.

    ![Panel för översikt](media/saas-dbpertenant-log-analytics/overview.png)

1. Vyerna i lösningen rullar i sidled, med en egen inre rullningslist längst ner. Uppdatera sidan om det behövs.

1. Om du vill utforska sammanfattningssidan markerar du panelerna eller enskilda databaser för att öppna en utforskare för detaljgranskning.

    ![Instrumentpanel för logganalys](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Ändra filterinställningen för att ändra tidsintervallet. För den här självstudien väljer du **Senaste 1 timmen**.

    ![Tidsfilter](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Välj en enskild databas för att utforska frågeanvändning och mått för databasen.

    ![Databasanalys](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Om du vill visa användningsmått bläddrar du analyssidan åt höger.
 
     ![Databasmått](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Bläddra analyssidan åt vänster och välj serverpanelen i listan **Resursinformation.**  

    ![Lista Resursinformation](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    En sida öppnas som visar pooler och databaser på servern.

    ![Server med pooler och databaser](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Välj en pool. På poolsidan som öppnas bläddrar du åt höger för att se poolmåtten. 

    ![Poolmått](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Tillbaka till log analytics-arbetsytan väljer du **OMS Portal** för att öppna arbetsytan där.

    ![Log Analytics-arbetsyta](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

På arbetsytan Log Analytics kan du utforska logg- och måttdata ytterligare. 

Övervakning och avisering i Azure Monitor-loggar baseras på frågor över data på arbetsytan, till skillnad från aviseringar som definierats för varje resurs i Azure-portalen. Genom att basera aviseringar på frågor kan du definiera en enda avisering som tittar över alla databaser i stället för att definiera en per databas. Frågor begränsas endast av de data som är tillgängliga på arbetsytan.

Mer information om hur du använder Azure Monitor-loggar för att fråga och ange aviseringar finns [i Arbeta med aviseringsregler i Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Azure Monitor loggar för SQL Database-avgifter baserat på datavolymen på arbetsytan. I den här självstudien har du skapat en kostnadsfri arbetsyta, som är begränsad till 500 MB per dag. När den gränsen har uppnåtts läggs data inte längre till på arbetsytan.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Installera och konfigurera Azure Monitor-loggar.
> * Använd Azure Monitor-loggar för att övervaka pooler och databaser.

Prova [självstudien för klientanalys](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på den ursprungliga Wingtip Tickets SaaS-databas-per-klient-programdistributionen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-loggar](../azure-monitor/insights/azure-sql.md)
