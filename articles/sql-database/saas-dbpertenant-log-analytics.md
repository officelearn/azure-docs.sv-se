---
title: Använda Azure Monitor-loggar med en SQL-databas fleranvändarapp | Microsoft Docs
description: Konfigurera och använda Azure Monitor-loggar med en multitenant Azure SQL Database SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b283f2691d2cb3135007a752348a8d9759e870f5
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214171"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Konfigurera och använda Azure Monitor-loggar med en multitenant SQL Database SaaS-app

I den här självstudien får du konfigurera och använda [Azure Monitor loggar](/azure/log-analytics/log-analytics-overview) att övervaka elastiska pooler och databaser. Den här självstudien bygger på den [guiden för övervakning och hantering av prestanda](saas-dbpertenant-performance-monitoring.md). Den visar hur du använder Azure Monitor-loggar för att utöka den övervakning och avisering tillhandahålls i Azure-portalen. Azure Monitor loggar har stöd för övervakning av tusentals elastiska pooler och hundratusentals databaser. Azure Monitor-loggar tillhandahåller en enskild övervakningslösning som kan integrera övervakning av olika program och Azure-tjänster över flera Azure-prenumerationer.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Installera och konfigurera Azure Monitor-loggar.
> * Använda Azure Monitor-loggar att övervaka pooler och databaser.

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas-per-klient appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS databas-per-klient programmet](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Se den [guiden för övervakning och hantering av prestanda](saas-dbpertenant-performance-monitoring.md) för en diskussion om SaaS-scenarierna och mönstren och hur de påverkar kraven för en övervakningslösning.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Övervaka och hantera databaser och elastiska pool prestanda med Azure Monitor-loggar

För Azure SQL Database är övervakning och avisering tillgängligt för databaser och pooler i Azure-portalen. Den här inbyggda övervakningen och aviseringen är praktiskt, men det är också resursspecifika. Det innebär att det passar mindre bra för att övervaka stora installationer eller tillhandahålla en enhetlig vy över resurser och prenumerationer.

För scenarion med stora volymer, kan du använda Azure Monitor-loggar för övervakning och avisering. Azure Monitor är en separat Azure-tjänst som möjliggör analys över diagnostikloggar och telemetri som samlas in i en arbetsyta från potentiellt många tjänster. Azure Monitor-loggar tillhandahåller ett inbyggt frågespråk och visualiseringsverktyg som tillåter operationell dataanalys. SQL Analytics-lösningen innehåller flera fördefinierade elastisk pool och databasövervakning och aviseringar och frågor. Azure Monitor-loggar innehåller också en designer för anpassade vyer.

OMS-arbetsytor kallas nu för Log Analytics-arbetsytor. Log Analytics-arbetsytor och Analyslösningar öppna i Azure-portalen. Azure-portalen är den nya åtkomstpunkten, men det kan vara vad som finns bakom Operations Management Suite-portalen på vissa områden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Skapa prestanda diagnostiska data genom att simulera en arbetsbelastning på klienterna 

1. I PowerShell ISE öppnar *... \\WingtipTicketsSaaS-MultiTenantDb-master\\inlärningsmoduler\\prestandaövervakning och hantering\\Demo-PerformanceMonitoringAndManagement.ps1*. Lämna det här skriptet öppet eftersom du kan vilja köra flera av belastningen generation scenarier under den här självstudien.
1. Om du inte redan har gjort det, kan du etablera en batch med klienter för att se övervakningen kontexten mer intressant. Den här processen tar några minuter.

   a. Ange **$DemoScenario = 1**, _etablera en batch med klienter_.

   b. Tryck på F5 för att köra skriptet och distribuera en ytterligare 17 klienter.

1. Nu starta belastningsgeneratorn för att köra en simulerad belastning på alla klienter.

    a. Ange **$DemoScenario = 2**, _generera normal intensitet (ca 30 DTU)_.

    b. Tryck på F5 för att köra skriptet.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip biljetter SaaS databas-per-klient programskript

Wingtip biljetter SaaS med flera klienter databasskripten och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Anvisningar om att ladda ned och avblockera Wingtip biljetter PowerShell-skript finns i den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installera och konfigurera Log Analytics-arbetsytan och Azure SQL Analytics-lösningen

Azure Monitor är en separat tjänst som måste konfigureras. Azure Monitor loggar samlar in loggdata, telemetri och mått i Log Analytics-arbetsytan. Precis som andra resurser i Azure måste du skapa en Log Analytics-arbetsyta. Om arbetsytan behöver inte skapas i samma resursgrupp som programmen den övervakar. Gör så ofta som är lämpligast dock. Använda en enskild resursgrupp för att kontrollera att arbetsytan har tagits bort med programmet för appen Wingtip biljetter.

1. I PowerShell ISE öppnar *... \\WingtipTicketsSaaS-MultiTenantDb-master\\inlärningsmoduler\\prestandaövervakning och hantering\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Tryck på F5 för att köra skriptet.

Nu kan du öppna Azure Monitor-loggar i Azure-portalen. Det tar några minuter att samla in telemetri i Log Analytics-arbetsytan och göra den synlig. Ju längre lämnar du systemet samla in diagnostikdata, Ju mer intressant upplevelsen är. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Använd Log Analytics-arbetsyta och SQL Analytics-lösningen för att övervaka pooler och databaser


Öppna Log Analytics-arbetsyta i Azure-portalen och titta på telemetri som samlats in för databaser och pooler i den här övningen.

1. Bläddra till [Azure-portalen](https://portal.azure.com). Välj **alla tjänster** att öppna Log Analytics-arbetsyta. Sök sedan efter Log Analytics.

   ![Öppna Log Analytics-arbetsyta](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Välj arbetsytan med namnet _wtploganalytics -&lt;användaren&gt;_.

1. Välj **översikt** att öppna log analytics-lösningen i Azure-portalen.

   ![Översikt](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Det kan ta några minuter innan lösningen är aktiv. 

1. Välj den **Azure SQL Analytics** panelen för att öppna den.

    ![Översiktsikon](media/saas-dbpertenant-log-analytics/overview.png)

1. Vyerna i lösningen rulla åt sidan, med sina egna inre rullningslist längst ned på sidan. Uppdatera sidan om det behövs.

1. Om du vill utforska sammanfattningssidan, Välj paneler eller enskilda databaser för att öppna en detaljnivå explorer.

    ![Log analytics-instrumentpanelen](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Ändra filtret inställningen för att ändra tidsintervallet. Den här självstudien väljer **senaste 1 timme**.

    ![Tidsfiltret](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Välj en individuell databas att utforska fråga användnings- och mått för den här databasen.

    ![Databasen analytics](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Om du vill se användningsstatistik, bläddrar du analyssidan till höger.
 
     ![Databasmått](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Bläddra på analytics-sidan till vänster och välj panelen server i den **resursinformation** lista.  

    ![Resurslistan Info](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    En sida öppnas som visar pooler och databaser på servern.

    ![Server med pooler och databaser](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Välj en pool. Bläddra till höger för att se mått som poolen på sidan som öppnas för poolen. 

    ![Pool-mått](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Tillbaka i Log Analytics-arbetsyta väljer **OMS-portalen** att öppna det på arbetsytan.

    ![Log Analytics-arbetsyta](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Du kan utforska loggen och mätdata data ytterligare i Log Analytics-arbetsytan. 

Övervakning och aviseringar i Azure Monitor är loggar baserade på frågor över data i arbetsytan, till skillnad från aviseringar som definieras för varje resurs i Azure-portalen. Du kan basera aviseringar på frågor, för att definiera en avisering som söker i alla databaser, i stället för att definiera en per databas. Frågor begränsas bara av data som är tillgängliga på arbetsytan.

Mer information om hur du använder Azure Monitor-loggar att fråga och ställa in aviseringar finns i [fungerar med aviseringsregler i Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Azure Monitor-loggar för SQL Database-avgifter baserat på datavolymen i arbetsytan. I den här självstudien skapade du en kostnadsfri arbetsyta, som är begränsad till 500 MB per dag. När den gränsen har uppnåtts, läggs inte längre data till arbetsytan.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Installera och konfigurera Azure Monitor-loggar.
> * Använda Azure Monitor-loggar att övervaka pooler och databaser.

Prova den [klientanalys](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på distribuering för Wingtip biljetter SaaS-program för databas-per-klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-loggar](../azure-monitor/insights/azure-sql.md)
