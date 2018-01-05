---
title: "Hantera Azure SQL Database-schema i en app för flera klienter | Microsoft Docs"
description: "Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Hantera schemat för flera klienter i ett program för flera innehavare som använder Azure SQL Database

Den här självstudiekursen undersöker utmaningarna med att underhålla en potentiellt massiv flotta med databaser i en programvara som en tjänst (SaaS) i molnet. Lösningar visas för att hantera schemat förbättringar som utvecklas och genomförs under en app.

Då alla program utvecklas, ändringar kan uppstå till dess tabellkolumner eller annan schemaändring eller till dess referensdata eller relaterade objekt för prestanda. Med en SaaS-app måste ändringarna distribueras på ett samordnat sätt över flera befintlig klient-databaser. Och dessa ändringar måste ingå i framtida klient-databaser som läggs till appen. Därför måste ändringarna även införlivas i processen som etablerar nya databaser.

#### <a name="two-scenarios"></a>Två scenarier

Den här självstudiekursen utforskar följande två scenarier:
- Distribuera referens datauppdateringar för alla klienter.
- Retuning ett index på den tabell som innehåller referensdata.

Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktion i Azure SQL-databas som används för att utför dessa åtgärder i klient-databaser. Jobben fungerar även med gyllene mallen klient-databasen. Denna mall används när nya databaser etableras.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett konto för jobbet.
> * Fråga över flera innehavare.
> * Uppdatera data i alla klient-databaser.
> * Skapa ett index i en tabell i alla klient-databaser.

## <a name="prerequisites"></a>Förutsättningar

- Appen Wingtip biljetter måste redan distribueras:
    - Instruktioner finns i första självstudierna, vilket ger den *Wingtip biljetter* SaaS flera innehavare databasen app:<br />[Distribuera och utforska ett delat flera innehavare program som använder Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Distribuera processen körs för mindre än fem minuter.
    - Du måste ha den *delat flera innehavare* version av Wingtip installerad. Versioner för *fristående* och *databas per klient* stöder inte finns kursen.

- Den senaste versionen av SQL Server Management Studio (SSMS) måste installeras. [Hämta och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell måste vara installerat. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Den här kursen använder funktioner för Azure SQL Database-tjänsten som är i en begränsad förhandsgranskning ([elastisk databas jobb](sql-database-elastic-database-client-library.md)). Om du vill göra den här självstudiekursen, ange ditt prenumerations-ID till  *SaaSFeedback@microsoft.com*  = elastiska jobb Preview med ämnet. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta  *SaaSFeedback@microsoft.com*  för frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemat management mönster

Delat flera innehavare databasmodellen används i det här exemplet aktiverar en innehavare databas som innehåller en eller flera innehavare. Det här exemplet utforskar möjligt att använda en blandning av en många-klient och en klient databaser aktiverar en *hybrid* modell för hantering av klienter. Det är komplicerat att hantera dessa databaser. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av SQL-datanivån. Jobb kan du säkert och tillförlitligt sätt köra Transact-SQL-skript som aktiviteter mot en grupp av klient-databaser. Uppgifter som är oberoende av användarinteraktion eller indata. Den här metoden kan användas för att distribuera ändringarna till schemat eller vanliga referensdata över alla klienter i ett program. Elastiska jobb kan också användas för att underhålla en gyllene mallen kopia av databasen. Mallen används för att skapa nya klienter, se alltid till senaste schemat och referensdata som används.

![skärmen](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Med integrerad innebär det krävs inga ytterligare tjänster eller komponenter. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Begränsad preview stöder för närvarande PowerShell för att skapa jobb konton och T-SQL för att skapa och hantera jobb.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip biljetter SaaS flera innehavare databasen programmets källkod och skript

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) databasen på Github. Finns det [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Skapa en jobbkonto-databas och ett nytt jobbkonto

Den här kursen kräver att du använder PowerShell för att skapa databas för jobbet och kontot för jobb. Som MSDB-databas som används av SQL Agent, använder en Azure SQL database elastiska jobb för att lagra jobbdefinitioner, jobbstatus och tidigare. När jobbet kontot har skapats kan du skapa och övervaka jobb omedelbart.

1. I **PowerShell ISE**öppnar *... \\Learning moduler\\schemat Management\\Demo-SchemaManagement.ps1*.
2. Tryck **F5** för att köra skriptet.

Den *Demo-SchemaManagement.ps1* skript anrop av *distribuera SchemaManagement.ps1* skript för att skapa en nivå *S2* databas med namnet **jobaccount** på katalogservern. Skriptet skapar sedan kontot jobbet skicka jobaccount databasen som en parameter till jobbet kontot skapa-anrop.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

#### <a name="prepare"></a>Förbereda

Varje innehavare databasen innehåller en uppsättning platsen typer i den **VenueTypes** tabell. Plats-typer definiera vilken typ av händelser som finns på en plats. I den här övningen du distribuerar en uppdatering för alla databaser att lägga till två ytterligare plats typer: *motorcykel tävling* och *simning en*. De här platstyperna motsvarar bakgrundsbilden du ser i klienternas evenemangsapp.

Innan du distribuerar nya referensdata notera antalet typer av plats som redan finns, vilket kan vara 10. Notera genom att klicka på följande länk till Wingtip webbgränssnittet och sedan klicka på den **plats typen** listrutan:
- http://events.Wingtip-MT.<USER>. trafficmanager.net

Nu kan du räkna antalet den ursprungliga platsen typer. Observera att särskilt varken *motorcykel tävling* eller *simning en* finns ännu.

#### <a name="steps"></a>Steg

Nu du skapar ett jobb för att uppdatera den **VenueTypes** tabell i varje databas för klienter, genom att lägga till två nya plats-typer.

Om du vill skapa ett nytt jobb du använder en uppsättning jobb systemets lagrade procedurer som har skapats i den *jobaccount* databas. Förfaranden som skapades när jobbet kontot skapades.

1. Anslut till klient-servern i SSMS,: tenants1-huvudmålservern -\<användaren\>. database.windows.net

2. Bläddra till den *tenants1* databasen på den *tenants1-huvudmålservern -\<användare\>. database.windows.net* server.

3. Frågan i *VenueTypes* tabell för att bekräfta att *motorcykel tävling* och *simning en* ännu inte i resultatlistan.

4. Ansluta till katalogserver, vilket är *katalog-huvudmålservern -\<användare\>. database.windows.net*.

5. Ansluta till den *jobaccount* databasen i katalogservern.

6. Öppna filen i SSMS, *... \\Learning moduler\\schemat Management\\DeployReferenceData.sql*.

7. Ändra instruktionen: Ange @User = &lt;användaren&gt; och Ersätt det användar-värde som används när du distribuerade databasprogram Wingtip biljetter SaaS flera innehavare.

8. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i den *DeployReferenceData.sql* skript:

- **SP\_lägga till\_mål\_grupp** skapar namnet på målgrupp *DemoServerGroup*, och lägger till målet medlemmar i gruppen.

- **SP\_lägga till\_mål\_grupp\_medlem** lägger till följande objekt:
    - En *server* måltyp medlem.
        - Det här är den *tenants1-huvudmålservern -&lt;användare&gt;*  server som innehåller databaserna som klienter.
        - Därmed ingår alla databaser på servern i jobbet när jobbet körs.
    - En *databasen* mål Medlemstyp för gyllene databasen (*basetenantdb*) som finns på *katalog-huvudmålservern -&lt;användare&gt;*  server
    - En *databasen* måltyp medlem att inkludera den *adhocreporting* databas som används i en senare kursen.

- **SP\_lägga till\_jobbet** skapas ett jobb som kallas *referens Data distribution*.

- **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommandot om du vill uppdatera referenstabellen VenueTypes.

- De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts. Jobbet uppdateras databasen klienter, samt två nya databaser som innehåller referenstabellen.

SSMS, bläddra till klient-databasen på den *tenants1-huvudmålservern -&lt;användare&gt;*  server. Frågan i *VenueTypes* tabell för att bekräfta att *motorcykel tävling* och *simning en* nu lagts till i tabellen. Totalt antal typer av platsen bör har ökat med två.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen liknar föregående övning. Den här övningen skapar ett jobb för att återskapa indexet för referens tabellens primärnyckel. Återskapa ett index är en typisk management databasåtgärd som en administratör kan köras när en belastningen för stora mängder data i en tabell för att förbättra prestanda.

1. Anslut till i SSMS, *jobaccount* databasen i *katalog-huvudmålservern -&lt;användare&gt;. database.windows.net* server.

2. Öppna i SSMS, *... \\Learning moduler\\schemat Management\\OnlineReindex.sql*.

3. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i den *OnlineReindex.sql* skript:

* **SP\_lägga till\_jobbet** skapar ett nytt jobb som kallas *Online indexera PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommando för att uppdatera indexet.

* De återstående vyerna i skriptet övervaka jobbkörning av. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på alla mål-gruppmedlemmar.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Skapa ett konto för jobbet att fråga på flera innehavare.
> - Uppdatera data i alla klient-databaser.
> - Skapa ett index i en tabell i alla klient-databaser.

Försök med den [Ad hoc-rapportering kursen](saas-multitenantdb-adhoc-reporting.md).

