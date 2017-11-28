---
title: "Använda Log Analytics med en SQL Database-app för flera klienter | Microsoft Docs"
description: "Konfigurera och använda logganalys (OMS) med en Azure SQL Database SaaS app för flera innehavare"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 48e8eb91a5febcc1109bee3404bb534bd0391f88
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Konfigurera och använda logganalys (OMS) med en Azure SQL Database SaaS-app för flera innehavare

I kursen får du konfigurera och använda *logganalys ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* för övervakning av elastiska pooler och databaser. Den här kursen bygger på den [prestandaövervakning och hantering av kursen](saas-dbpertenant-performance-monitoring.md). Den visar hur du använder *logganalys* att utöka den övervakning och avisering i Azure-portalen. Logganalys är lämplig för övervakning och avisering i skala, eftersom den stöder hundratals pooler och hundratals tusentals databaser. Den erbjuder också en enskild övervakningslösning som kan integrera övervakning av olika program och Azure-tjänster över flera Azure-prenumerationer.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Installera och konfigurera Log Analytics (OMS)
> * Använda Log Analytics för att övervaka pooler och databaser

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS databas Per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Se [guiden prestandaövervakning och hantering](saas-dbpertenant-performance-monitoring.md) för en diskussion om SaaS-scenarierna och mönstren och hur de påverkar kraven för en övervakningslösning.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Övervaka och hantera prestanda med Log Analytics (OMS)

För SQL Database, finns övervakning och avisering tillgängligt för databaser och pooler. Den här inbyggda övervakningen och aviseringen är resursspecifik och praktiskt för små antal resurser, men lämpar sig sämre för övervakning av stora installationer eller för att ge en enhetlig vy över olika resurser och prenumerationer.

För scenarion med stora volymer, kan Log Analytics användas. Det här är en separat Azure-tjänst som tillhandahåller analys för utgivna diagnostikloggar och telemetri som samlats in i en arbetsyta för logganalys. Den samlar telemetri från flera olika tjänster som sedan kan användas för att fråga och ställa in aviseringar. Log Analytics tillhandahåller ett inbyggt frågespråk och verktyg för datavisualisering som tillåter operationell dataanalys och visualisering. SQL Analytics lösningen innehåller flera fördefinierade elastisk pool och databasen övervakning och avisering vyer och frågor och kan du lägga till egna ad hoc-frågor och spara dem efter behov. OMS innehåller också en designer för anpassade vyer.

Log Analytics arbetsytor och analyslösningar kan öppnas både i Azure-portalen och i OMS. Azure-portalen är den nyare åtkomstpunkten, men kan ligga efter OMS-portalen på vissa områden.

### <a name="create-data-by-starting-the-load-generator"></a>Skapa data genom att starta belastningen generator 

1. I den **PowerShell ISE**öppnar **Demo-PerformanceMonitoringAndManagement.ps1**. Håll det här skriptet öppet då du kan vilja köra flera scenarion med belastningsgenerering under den här guiden.
1. Om du har mindre än fem klienter övervakning etablera en grupp med klienter för att ge en mer intressant kontext:
   1. Ställ in **$DemoScenario = 1,** **Etablera en batch med klienter**
   1. Kör skriptet genom att trycka på **F5**.

1. Ange **$DemoScenario** = 2, **generera normal styrka belastning (cirka 40 DTU)**.
1. Kör skriptet genom att trycka på **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installera och konfigurera Log Analytics och sedan Azure SQL Analytics-lösningen

Log Analytics är en separat tjänst som måste konfigureras. Log Analytics samlar in loggdata och telemetri och mått i arbetsyta för logganalys. En arbetsyta är en resurs, precis som andra resurser i Azure och måste skapas. Även om arbetsytan inte behöver skapas i samma resursgrupp som programmen den övervakar, känns det ofta mest logiskt. Detta gör arbetsytan ska raderas enkelt med programmet genom att ta bort resursgruppen för Wingtip biljetter SaaS databas Per klient SaaS-app.

1. I den **PowerShell ISE**öppnar... \\Learning moduler\\prestandaövervakning och hantering av\\logga Analytics\\*Demo-LogAnalytics.ps1*.
1. Kör skriptet genom att trycka på **F5**.

Nu bör du kunna öppna logganalys i Azure-portalen (eller OMS-portalen). Det tar några minuter för telemetri samlas i logganalys-arbetsytan och blir synlig. Ju längre lämna du systemet datainsamling ju mer intressant av är. Ett bra tillfälle att hämta något att dricka. Se bara till att belastningsgeneratorn är igång!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Använd Log Analytics och SQL Analytics-lösningen för att övervaka pooler och databaser


Öppna Log Analytics och OMS-portalen för att titta på telemetrin som samlas in för databaser och pooler i den här övningen.

1. Bläddra till [Azure-portalen](https://portal.azure.com) och öppna Log Analytics genom att klicka på fler tjänster och därefter söka efter Log Analytics:

   ![öppna log analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Välj arbetsytan med namnet *wtploganalytics-&lt;användare&gt;*.

1. Välj **översikt** för att öppna Log Analytics-lösningen i Azure-portalen.
   ![översiktslänk](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Det kan ta några minuter innan lösningen är aktiv. Ha tålamod!

1. Klicka på Azure SQL Analytics-ikonen för att öppna den.

    ![översikt](media/saas-dbpertenant-log-analytics/overview.png)

    ![analys](media/saas-dbpertenant-log-analytics/analytics.png)

1. Vyn i lösningens blad rullar åt sidan, med sin egen rullningslist längst ned (uppdatera bladet vid behov).

1. Utforska de olika vyerna genom att klicka på dem eller på enskilda resurser för att gå in i detalj där du kan använda tidsreglaget i övre vänstra hörnet, eller klicka på en lodrät stapel för att fokusera på ett snävare tidsintervall. Med den här vyn, kan du välja enskilda databaser eller pooler för att fokusera på specifika resurser:

    ![diagram](media/saas-dbpertenant-log-analytics/chart.png)

1. Tillbaka till lösningsbladet, kan du bläddra längst till höger för att se några sparade frågor som du kan klicka på för att öppna och utforska. Du kan experimentera med att ändra dessa och spara intressanta frågor som du skapar som du kan öppna och använda med andra resurser.

1. Tillbaka i Log Analytics-arbetsytebladet, väljer du OMS-portalen för att öppna lösningen där.

    ![oms](media/saas-dbpertenant-log-analytics/oms.png)

1. I OMS-portalen kan du konfigurera aviseringar. Klicka på aviseringsdelen av databasens DTU-vy.

1. I loggsökningsvyn som visas, ser du ett stapeldiagram med de mått som representeras.

    ![loggsökning](media/saas-dbpertenant-log-analytics/log-search.png)

1. Om du klickar på aviseringen i verktygsfältet, kommer att kunna se aviseringskonfigurationen och kan ändra den.

    ![lägg till aviseringsregel](media/saas-dbpertenant-log-analytics/add-alert.png)

Övervakning och aviseringar i Log Analytics och OMS baseras på frågor över data i arbetsytan, till skillnad från aviseringar på varje resursblad, som är resursspecifika. Därmed kan du definiera en avisering som söker i alla databaser, istället för att exempelvis definiera en per databas. Eller skriva en avisering som använder en sammansatt fråga över flera resurstyper. Frågor begränsas bara av de data som finns tillgängliga i arbetsytan.

Log Analytics för SQL Database debiteras baserat på datavolymen i arbetsytan. I den här guiden får skapat du en kostnadsfri arbetsyta, som är begränsad till 500MB per dag. När den gränsen har uppnåtts, läggs inte längre data till i arbetsytan.


## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]
> * Installera och konfigurera Log Analytics (OMS)
> * Använda Log Analytics för att övervaka pooler och databaser

[Guide för klientanalys](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på inledande programdistributionen Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
