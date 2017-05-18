---
title: "Köra adhoc-analysfrågor över alla klienter (SaaS-exempelapp med Azure SQL Database) | Microsoft Docs"
description: "Köra adhoc-analysfrågor över alla klienter"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c00c25116002c46fc94c5ea59acc101a2055b689
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Köra adhoc-analysfrågor över alla WTP-klienter

I den här självstudiekursen får du skapa en databas för adhoc-analys och köra flera frågor över alla klienter. Med dessa frågor kan du hämta kunskap från information som döljer sig bland WTP-appens dagliga driftdata.

När adhoc-analysfrågor (över flera klienter) körs med WTP-appen används [Elastic Query](sql-database-elastic-query-overview.md) tillsammans med en analysdatabas.


I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Distribuera en databas för adhoc-analys
> * Köra distribuerade frågor över alla klientdatabaser



Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* WTP-appen ska ha distribuerats. Du kan distribuera appen på mindre än fem minuter genom att följa anvisningarna i [Distribuera och utforska WTP SaaS-appen](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Adhoc-analysmönster

En av fördelarna med SaaS-appar är att du kan dra nytta av de stora mängder klientdata som du har sparat centralt i molnet. Du kan använda dessa data för att få information om verksamheten, användningen av dina appar och klienter samt om användarna och deras preferenser och beteenden. Du kan utnyttja dessa insikter till att utveckla nya funktioner, förbättra användbarheten och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. Ett tillvägagångssätt är att använda Elastic Query, som gör att du kan ställa adhoc-frågor över en distribuerad uppsättning databaser med ett gemensamt schema. Elastic Query använder en *huvuddatabas*. I huvuddatabasen definieras externa tabeller som speglar tabellerna i de distribuerade databaserna (klientdatabaserna). Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastic Query använder shardkartan i katalogdatabasen för att ange platsen för klientdatabaserna. Konfiguration och frågor sker med vanlig T-SQL och du kan använda adhoc-frågor från verktyg som Power BI och Excel.

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskript

Wingtip Tickets-skript och programmets källkod finns tillgängligt från github-repon [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Skriptfilerna finns i [mappen Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Ladda ner mappen **Learning Modules** till din lokala dator med mappstrukturen intakt.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Distribuera databasen som används för adhoc-analysfrågor

I den här övningen distribueras en adhoc-analysdatabas som innehåller ett schema som används för att utfärda adhoc-frågor över alla klientdatabaser.

1. Öppna ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* i *PowerShell ISE* och ange följande värden:
   * **$DemoScenario** = 2, **Distribuera adhoc-analysdatabas**.

1. Tryck på **F5** för att köra skriptet och skapa en ny SQL-databas för adhoc-analys och lägg till den i WTP-katalogen. Tabellerna TicketPurchases, Tickets och Venues läggs till som externa tabeller som du kan köra frågor på.
   Det är ok om du får varningar nu om att *RPC-ser inte är tillgänglig*.


Nu har du en *adhocanalytics*-databas som du kan köra distribuerade frågor på och samla information över alla klienter från!

## <a name="run-ad-hoc-analytics-queries"></a>Köra adhoc-analysfrågor

I den här övningen körs adhoc-analysfrågor för att hämta kunskap om klienter från WTP-appen.

1. Öppna ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* i SSMS.
1. Se till att du är ansluten till **adhocanalytics**-databasen
1. Välj en fråga som du vill köra och tryck på **F5**:

    ![DocumentDB](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Distribuera en databas för adhoc-analys
> * Köra distribuerade frågor över alla klientdatabaser

[Självstudiekurs i Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på den första programdistributionen av Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)

