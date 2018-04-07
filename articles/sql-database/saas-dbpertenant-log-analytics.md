---
title: Använda logganalys med en SQL-databas multitenant appen | Microsoft Docs
description: Konfigurera och använda logganalys med en multitenant Azure SQL Database SaaS-app
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 285b8d0acc8a6cbe1a6441a4aabf372de204309e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Konfigurera och använda logganalys med en multitenant SQL-databasen SaaS-app

I kursen får du konfigurera och använda Azure [logganalys](/azure/log-analytics/log-analytics-overview) övervaka elastiska pooler och databaser. Den här kursen bygger på den [prestanda övervakning och hantering av kursen](saas-dbpertenant-performance-monitoring.md). Den visar hur du använder logganalys för att utöka övervakning och aviseringar som anges i Azure-portalen. Logganalys stöder övervakning tusentals elastiska pooler och hundratals tusentals databaser. Log Analytics tillhandahåller en enda övervakningslösning som kan integrera övervakning av olika program och Azure-tjänster över flera Azure-prenumerationer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Installera och konfigurera logganalys.
> * Använda Log Analytics för att övervaka pooler och databaser.

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip biljetter SaaS databas per klient programmet](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Finns det [prestanda övervakning och hantering av kursen](saas-dbpertenant-performance-monitoring.md) en beskrivning av SaaS-scenarier och mönster och hur de påverkar kraven på en övervakningslösning.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Övervaka och hantera databaser och elastiska poolen prestanda med logganalys

För Azure SQL Database finns övervakning och avisering på databaser och pooler i Azure-portalen. Den här inbyggda övervakning och avisering är praktiskt, men det är också resursspecifika. Det innebär att det är mindre väl passar för att övervaka större installationer eller tillhandahålla en samlad bild över resurser och -prenumerationer.

Du kan använda Log Analytics för övervakning och avisering för scenarier med stora volymer. Log Analytics är en separat Azure-tjänst som aktiverar analytics över diagnostiska loggar och telemetri som har samlats in i en arbetsyta från potentiellt många tjänster. Logganalys innehåller inbyggda frågan språk och data visualiseringen-verktyg som gör att användningsdata analytics. SQL-Analytics lösningen innehåller flera fördefinierade elastisk pool och databasen övervakning och aviseringar vyer och frågor. Logganalys innehåller också en anpassad vy designer.

Loggen arbetsytorna och analytics Analyslösningar öppna i Azure-portalen och Operations Management Suite. Azure portal är nyare åtkomstpunkt, men det kan vara bakom Operations Management Suite-portalen i vissa områden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Skapa diagnostikdata prestanda genom att simulera en arbetsbelastning på klienterna 

1. I PowerShell ISE öppnar *... \\WingtipTicketsSaaS MultiTenantDb master\\Learning moduler\\prestandaövervakning och hantering av\\Demo-PerformanceMonitoringAndManagement.ps1*. Behåll det här skriptet öppna eftersom kanske du vill köra flera belastningen generation scenarier under den här kursen.
2. Om du inte redan har gjort det, kan du etablera en grupp med klienter för att övervaka kontexten mer intressant. Den här processen tar några minuter.

   a. Ange **$DemoScenario = 1**, _etablera en grupp med klienter_.

   b. Tryck på F5 för att köra skriptet och distribuera en ytterligare 17 innehavare.

3. Nu starta belastningen generatorn för att köra en simulerad belastning på alla klienter.

    a. Ange **$DemoScenario = 2**, _generera normal styrka belastning (cirka 30 DTU)_.

    b. Tryck på F5 för att köra skriptet.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas per klient

Wingtip biljetter SaaS multitenant databasskript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Steg för att ladda ned och avblockera Wingtip biljetter PowerShell-skript, finns det [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Installera och konfigurera logganalys och Azure SQL Analytics-lösning

Log Analytics är en separat tjänst som måste konfigureras. Logganalys samlar in loggdata, telemetri och mått i logganalys-arbetsytan. Precis som andra resurser i Azure måste logganalys-arbetsytan skapas. Arbetsytan behöver inte skapas i samma resursgrupp som de program som övervakas. Gör så ofta blir bäst om. Använda en enskild resursgrupp för att säkerställa att arbetsytan har tagits bort med programmet för Wingtip biljetter-app.

1. I PowerShell ISE öppnar *... \\WingtipTicketsSaaS MultiTenantDb master\\Learning moduler\\prestandaövervakning och hantering av\\logga Analytics\\** Demo-LogAnalytics.ps1***.
2. Tryck på F5 för att köra skriptet.

Nu kan du öppna Log Analytics i Azure-portalen eller Operations Management Suite-portalen. Det tar några minuter att samla in telemetri i logganalys-arbetsytan och göra den synlig. Ju längre låta du systemet samla in diagnostikdata, desto mer intressant av är. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Använd Log Analytics och SQL Analytics-lösningen för att övervaka pooler och databaser


Öppna Log Analytics och Operations Management Suite-portalen för att titta på telemetri som samlats in för databaser och pooler i den här övningen.

1. Bläddra till den [Azure-portalen](https://portal.azure.com). Välj **alla tjänster** att öppna Log Analytics. Sök sedan efter logganalys.

   ![Open Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Välj arbetsytan med namnet _wtploganalytics -&lt;användare&gt;_.

3. Välj **översikt** för att öppna Log Analytics-lösningen i Azure-portalen.

   ![Översikt](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Det kan ta några minuter innan lösningen är aktiv. 

4. Välj den **Azure SQL Analytics** rutan för att öppna den.

    ![Översikt över panelen](media/saas-dbpertenant-log-analytics/overview.png)

5. Vyerna i lösningen rulla åt sidan, med sina egna inre rullningslisten längst ned. Uppdatera sidan om det behövs.

6. Välj paneler eller enskilda databaser att öppna en nedåt explorer för att utforska på sammanfattningssidan.

    ![Instrumentpanelen för logg](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Ändra filterinställningen för att ändra tidsintervallet. Den här kursen väljer **senaste 1 timme**.

    ![tidsfiltret](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Välj en enskild databas att utforska frågan användnings- och mått för den här databasen.

    ![databasen analytics](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Rulla sidan analytics till höger om du vill se användningsstatistik.
 
     ![databasen mått](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Rulla sidan analytics till vänster och välj panelen server i den **resursinformationen** lista.  

    ![Lista över resurser Info](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    En sida öppnas som visar pooler och databaser på servern.

    ![Server med pooler och databaser](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Välj en pool. Bläddra till höger Se poolen mått på sidan som öppnas för poolen. 

    ![pool-mått](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. Välj tillbaka i logganalys-arbetsytan **OMS-portalen** att öppna arbetsytan det.

    ![Operations Management Suite-portalen sida vid sida](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Du kan utforska data loggen och mått i arbetsytan ytterligare Operations Management Suite-portalen. 

Övervakning och avisering i logganalys baseras på frågor över data på arbetsytan, till skillnad från aviseringar som definieras för varje resurs i Azure-portalen. Du kan basera aviseringar på frågor för att definiera en avisering som ser ut över alla databaser, i stället för att definiera en per databas. Frågor begränsas bara av data som är tillgängliga på arbetsytan.

Mer information om hur du använder logganalys att fråga och Ställ in aviseringar finns [arbeta med Varningsregler i logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics för SQL-databas avgifter baserat på datavolymen i arbetsytan. I kursen får skapat du en kostnadsfri arbetsyta som är begränsad till 500 MB per dag. När gränsen har nåtts, läggs inte längre data till arbetsytan.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Installera och konfigurera logganalys.
> * Använda Log Analytics för att övervaka pooler och databaser.

Försök i [klient analytics kursen](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på den första Wingtip biljetter SaaS databas per klient programdistributionen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
