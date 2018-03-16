---
title: "Använda Log Analytics med en SQL Database-app för flera klienter | Microsoft Docs"
description: "Konfigurera och använda logganalys (OMS) med en Azure SQL Database SaaS app för flera innehavare"
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: b141ca521ae9c4d9bf6a4be620bc8e5432c52f83
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Konfigurera och använda logganalys (OMS) med en Azure SQL Database SaaS-app för flera innehavare

I kursen får du konfigurera och använda *logganalys ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* för övervakning av elastiska pooler och databaser. Den här kursen bygger på den [prestandaövervakning och hantering av kursen](saas-dbpertenant-performance-monitoring.md). Den visar hur du använder *logganalys* att utöka den övervakning och avisering i Azure-portalen. Logganalys stöder övervakning tusentals elastiska pooler och hundratals tusentals databaser. Log Analytics tillhandahåller en enda övervakningslösning som kan integrera övervakning av olika program och Azure-tjänster över flera Azure-prenumerationer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Installera och konfigurera Log Analytics (OMS)
> * Använda Log Analytics för att övervaka pooler och databaser

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas Per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Se [guiden prestandaövervakning och hantering](saas-dbpertenant-performance-monitoring.md) för en diskussion om SaaS-scenarierna och mönstren och hur de påverkar kraven för en övervakningslösning.

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Övervaka och hantera prestanda för databasen och elastisk pool med logganalys eller Operations Management Suite (OMS)

För SQL-databas finns övervakning och avisering på databaser och pooler i Azure-portalen. Den här inbyggda övervakning och avisering är praktiskt, men som resursspecifika, det är mindre väl passar för övervakning av större installationer eller för att tillhandahålla en samlad bild över resurser och -prenumerationer.

Log Analytics kan användas för övervakning och avisering för scenarier med stora volymer. Log Analytics är en separat Azure-tjänst som aktiverar analytics över diagnostikloggar och telemetri som har samlats in i en arbetsyta från potentiellt många tjänster. Logganalys innehåller inbyggda frågan språk och data visualiseringsverktyg för att tillåta användningsdata analytics. SQL-Analytics lösningen innehåller flera fördefinierade elastisk pool och databasen övervakning och aviseringar vyer och frågor. OMS innehåller också en designer för anpassade vyer.

Log Analytics arbetsytor och analyslösningar kan öppnas både i Azure-portalen och i OMS. Azure-portalen är den nyare åtkomstpunkten, men kan ligga efter OMS-portalen på vissa områden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Skapa diagnostikdata prestanda genom att simulera en arbetsbelastning på klienterna 

1. I den **PowerShell ISE**öppnar *... \\WingtipTicketsSaaS MultiTenantDb master\\Learning moduler\\prestandaövervakning och hantering av\\** Demo-PerformanceMonitoringAndManagement.ps1***. Håll det här skriptet öppet då du kan vilja köra flera scenarion med belastningsgenerering under den här guiden.
1. Om du redan inte har gjort det, kan du etablera en grupp med klienter för att ge en mer intressant övervakning kontext. Detta tar några minuter:
   1. Ange **$DemoScenario = 1**, _etablera en grupp med klienter_
   1. Kör skript och distribuera en ytterligare 17 klienter genom att trycka på **F5**.  

1. Nu starta belastningen generatorn för att köra en simulerad belastning på alla klienter.  
    1. Ange **$DemoScenario = 2**, _generera normal styrka belastning (cirka 30 DTU)_.
    1. Kör skriptet genom att trycka på **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter PowerShell-skript.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installera och konfigurera Log Analytics och sedan Azure SQL Analytics-lösningen

Log Analytics är en separat tjänst som måste konfigureras. Logganalys samlar in loggdata, telemetri och mått i en log analytics-arbetsyta. Log analytics-arbetsyta är en resurs, precis som andra resurser i Azure, och måste skapas. När arbetsytan inte behöver skapas i samma resursgrupp som det/de program övervakar den, göra så ofta gör bäst. För Wingtip biljetter-app garanterar med en enskild resursgrupp arbetsytan tas bort med programmet.

1. I den **PowerShell ISE**öppnar *... \\WingtipTicketsSaaS MultiTenantDb master\\Learning moduler\\prestandaövervakning och hantering av\\logga Analytics\\** Demo-LogAnalytics.ps1***.
1. Kör skriptet genom att trycka på **F5**.

Nu bör du kunna öppna logganalys i Azure-portalen (eller OMS-portalen). Det tar några minuter för telemetri samlas i logganalys-arbetsytan och blir synlig. Ju längre låta du systemet samla in diagnostikdata ju mer intressant av är. Ett bra tillfälle att hämta något att dricka. Se bara till att belastningsgeneratorn är igång!

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Använd Log Analytics och SQL Analytics-lösningen för att övervaka pooler och databaser


Öppna Log Analytics och OMS-portalen för att titta på telemetrin som samlas in för databaser och pooler i den här övningen.

1. Bläddra till den [Azure-portalen](https://portal.azure.com) och öppna Log Analytics genom att klicka på **alla tjänster**, Sök sedan efter logganalys:

   ![öppna log analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Välj arbetsytan med namnet _wtploganalytics -&lt;användare&gt;_.

1. Välj **översikt** för att öppna Log Analytics-lösningen i Azure-portalen.

   ![Översikt över-länk](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Det kan ta några minuter innan lösningen är aktiv. Ha tålamod!

1. Klicka på Azure SQL Analytics-ikonen för att öppna den.

    ![översikt](media/saas-dbpertenant-log-analytics/overview.png)

    ![analys](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Vyerna i lösningen rulla åt sidan, med sina egna inre rullningslisten längst ned (uppdatera sidan om det behövs).

1. Utforska sammanfattningssidan genom att klicka på panelerna eller på en individuell databas att öppna en nedåt explorer.

1. Ändra inställningen för att ändra tidsintervallet - för den här självstudiekursen Välj filtret _senaste 1 timme_

    ![tidsfiltret](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Välj en enskild databas att utforska frågan användnings- och mått för den här databasen.

    ![databasen analytics](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Om du vill visa användningsinformation rulla mått sidan analytics till höger.
 
     ![databasen mått](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Rulla sidan analytics till vänster och klicka på panelen server i listan över resursinformationen. Detta öppnar sidan visar pooler och databaser på servern. 

     ![resursinformationen](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![Server med pooler och databaser](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Sidan som öppnas som visar pooler och databaser på servern, klickar du på poolen på servern.  Bläddra till höger Se poolen mått på sidan som öppnas för poolen.  

     ![pool-mått](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Tillbaka på logganalys-arbetsytan väljer **OMS-portalen** att öppna arbetsytan det.

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Du kan utforska data loggen och mått i arbetsytan ytterligare i OMS-portalen.  

Övervakning och avisering i logganalys och OMS baseras på frågor över data på arbetsytan, till skillnad från aviseringar som definieras för varje resurs i Azure-portalen. Du kan basera aviseringar på frågor för att definiera en avisering som ser ut över alla databaser, i stället för att definiera en per databas. Frågor begränsas bara av de data som finns tillgängliga i arbetsytan.

Mer information om hur du använder OMS att fråga och Ställ in aviseringar, se, [arbeta med Varningsregler i logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics för SQL Database debiteras baserat på datavolymen i arbetsytan. I kursen får skapat du en kostnadsfri arbetsyta som är begränsad till 500 MB per dag. När gränsen har nåtts kan läggs inte längre data till arbetsytan.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Installera och konfigurera Log Analytics (OMS)
> * Använda Log Analytics för att övervaka pooler och databaser

[Guide för klientanalys](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på inledande programdistributionen Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
