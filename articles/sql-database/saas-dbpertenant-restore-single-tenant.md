---
title: "Återställa en Azure SQL-databas i en app för flera innehavare SaaS | Microsoft Docs"
description: "Lär dig hur du återställer en enskild innehavare SQL-databas när data har tagits bort av misstag"
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
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 60788b8a1b417e9bdfbe5ea8424dff9a39f36f2d
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>Återställa en enskild innehavare Azure SQL-databas i en SaaS-app för flera innehavare

Wingtip SaaS-appar skapas med en databas per klient-modell där varje innehavare har sin egen databas. En av fördelarna med den här modellen är att det är lätt att återställa en enskild klient data i isolering utan att påverka andra klienter.

Lär dig två mönster för återställning av data i den här självstudiekursen:

> [!div class="checklist"]

> * Återställa en databas till en parallell databasen (sida-vid-sida)
> * Återställa en databas på plats


|||
|:--|:--|
| **Återställa klienten till en tidigare tidpunkt, till en parallell databas** | Det här mönstret kan användas av klienten för granskning, granskning kompatibilitet, osv. Den ursprungliga databasen är online och oförändrade. |
| **Återställa klient på plats** | Det här mönstret är vanligt att återställa en klient till en tidigare tidpunkt när en klient att av misstag tar bort data. Den ursprungliga databasen tas offline och ersätts med den återställda databasen. |
|||

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip SaaS-appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>Introduktion till SaaS-klient återställning mönster

För mönster för klient-återställning finns två enkla mönster för att återställa en enskild klientorganisation data. Eftersom klienten databaser är isolerade från varandra, har återställa en klient ingen inverkan på data för alla andra innehavare.

I det första mönstret återställs data till en ny databas. Klienten sedan får åtkomst till den här databasen tillsammans med deras produktionsdata. Det här mönstret gör det möjligt för en klientadministratör att granska data som återställs och eventuellt användas för att selektivt skriva över den aktuella värden. Den kan designer för SaaS-appen att fastställa hur avancerade återställningsalternativ data ska vara. Bara att kunna granska data i det tillstånd som det hade vid en viss tidpunkt kan vara allt som krävs i vissa scenarier. Om databasen använder [georeplikering](sql-database-geo-replication-overview.md), rekommenderar vi att kopiera nödvändiga data från den återställda kopian i den ursprungliga databasen. Om du ersätta den ursprungliga databasen med den återställda databasen måste du konfigurera om och omsynkronisera geo-replikering.

I det andra mönstret, vilket förutsätter att klienten har haft en förlust eller data skadas, återställs klientens produktionsdatabasen till en tidigare tidpunkt. Återställning i plats mönstret tas innehavaren offline under en kort tid när databasen har återställts och online igen. Den ursprungliga databasen tas bort, men kan fortfarande återställa från om du behöver gå tillbaka till en även tidigare tidpunkt. En variant av det här mönstret kan byta namn på databasen i stället för att ta bort den, även om byta namn på databasen innehåller inga ytterligare fördelar vad gäller datasäkerheten.

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskript

Wingtip SaaS-skript och programmets källkod är tillgängliga i den [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-lagringsplatsen. [Steg för att hämta Wingtip SaaS-skripten](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulera en klient oavsiktlig borttagning av data

För att demonstrera dessa scenarion för återställning, behöver vi *av misstag* ta bort vissa data i någon av klient-databaser. Du kan ta bort en post, konfigurerar nästa steg demonstrationen så att inte hämta blockeras av referensintegritet överträdelser! Det ger också vissa biljett köp data som du kan använda senare i den *Wingtip SaaS Analytics-självstudier*.

Kör skriptet biljett generator och skapa ytterligare data. Biljett-generatorn köpa avsiktligt inte biljetter för varje innehavare senaste händelse.

1. Öppna... \\Learning moduler\\verktyg\\*Demo-TicketGenerator.ps1* i den *PowerShell ISE*
1. Tryck på **F5** att köra skriptet och generera kunder och biljett försäljningsinformation.


### <a name="open-the-events-app-to-review-the-current-events"></a>Öppna appen händelser om du vill granska aktuella händelser

1. Öppna den *händelser hubb* (http://events.wtp.&lt; användaren&gt;. trafficmanager.net) och klicka på **Contoso samklang Hall**:

   ![evenemangshubben](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Bläddra i listan över händelser och anteckna den sista händelsen i listan:

   ![Senaste händelse](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>Kör demo-scenario för att av misstag tar bort den sista händelsen

1. Öppna... \\Learning moduler\\affärskontinuitet och Haveriberedskap\\RestoreTenant\\*Demo-RestoreTenant.ps1* i den *PowerShell ISE*, och anger följande värde:
   * **$DemoScenario** = **1**, inställd på **1** -ta bort händelser utan biljett försäljning.
1. Tryck på **F5** att köra skriptet och ta bort den sista händelsen. Du bör se ett bekräftelsemeddelande som liknar följande:

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso händelser sidan öppnas. Bläddra nedåt och kontrollera händelsen är borta. Om händelsen är fortfarande i listan, klicka på Uppdatera och kontrollera att det är borta.

   ![Senaste händelse](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Återställa en databas klient parallellt med produktionsdatabasen

Den här övningen återställer Contoso samklang Hall databasen till en tidpunkt innan händelsen har tagits bort. När händelsen har tagits bort i föregående steg, som du vill återställa och se data som tagits bort. Du behöver inte återställa produktionsdatabasen med den borttagna posten, men du behöver återställa den gamla databasen för den gamla data för andra affärsskäl.

 Den *återställning TenantInParallel.ps1* skriptet skapar en parallell klient-databasen och en parallell katalogpost både med namnet *ContosoConcertHall\_gamla*. Det här mönstret av återställning passar bäst för att återställa från en mindre dataförlust eller för efterlevnad och granskning återställningsscenarier. Det är också den rekommenderade metoden när du använder [georeplikering](sql-database-geo-replication-overview.md).

1. Slutför den [simulera en användare av misstag tar bort data](#simulate-a-tenant-accidentally-deleting-data) avsnitt.
1. Öppna... \\Learning moduler\\affärskontinuitet och Haveriberedskap\\RestoreTenant\\_Demo-RestoreTenant.ps1_ i den *PowerShell ISE*.
1. Ange **$DemoScenario** = **2**, anger du **2** till *återställning klient parallellt*.
1. Tryck **F5** för att köra skriptet.

Skriptet återställer databasen för klient (till en parallell databas) till en tidpunkt innan du tar bort händelsen i föregående avsnitt. Den skapar en andra databas, tar bort alla befintliga katalogen metadata som finns i den här databasen, och lägger till databasen till katalogen under den *ContosoConcertHall\_gamla* post.

Demo-skriptet öppnas sidan händelser i webbläsaren. Observera från URL: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` att detta visar data från den återställda databasen där *_old* har lagts till i namnet.

Rulla de händelser som visas i webbläsaren om du vill bekräfta att händelsen tas bort i föregående avsnitt har återställts.

Observera att exponera återställda innehavaren som en ytterligare klient med egna händelser app för att bläddra biljetter är sannolikt inte hur du vill ge en klient åtkomst till har återställt data, men används för att illustrera enkelt mönster för återställning.

I verkligheten, skulle du förmodligen bara behålla den här återställda databasen för en definierad period. Du kan ta bort posten återställda klient när du är klar genom att anropa den *ta bort RestoredTenant.ps1* skript.

1. Ange **$DemoScenario** till **4** att välja den *ta bort återställts klient* scenario.
1. **Köra** **med** **F5**
1. Den *ContosoConcertHall\_gamla* post nu tagits bort från katalogen. Gå vidare och stänga sidan händelser för den här klienten i webbläsaren.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Återställa en klient på plats, ersätter den befintliga databasen för klient

Den här övningen återställer Contoso samklang Hall-klient till en tidpunkt innan händelsen har tagits bort. Den *återställning TenantInPlace* skript återställer databasen för klient till en ny databas som pekar på en tidigare punkt i tiden och tar bort den ursprungliga databasen. Det här mönstret av återställning passar bäst för återställning från allvarliga datafel eftersom det kan finnas dataförluster som klienten måste få plats.

1. Öppna **Demo-RestoreTenant.ps1** filen i PowerShell ISE
1. Ange **$DemoScenario** till **5** att välja den *återställa klient i plats scenariot*.
1. Köra med **F5**.

Skriptet återställer databasen för klient till en fem minuter innan händelsen har tagits bort. Detta sker första tar Contoso samklang Hall innehavaren offline så att det finns ingen ytterligare uppdateringar till data. Sedan en parallell databas genom att återställa från återställningspunkten som namnges med en tidsstämpel så databasnamnet inte står i konflikt med befintliga klient-databasnamn. Sedan den gamla klient databasen tas bort och byta namn på den återställda databasen till det ursprungliga databasnamnet. Slutligen är Contoso samklang Hall online så att appen åtkomst till den återställda databasen.

Du återställt har databasen till en tidpunkt innan händelsen har tagits bort. Händelser sidan öppnas, så kontrollera senaste händelse har återställts.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Återställa en databas till en parallell databasen (sida-vid-sida)
> * Återställa en databas på plats

[Hantera databasschemat för klient](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md)
* [Lär dig mer om säkerhetskopiering av SQL-databaser](sql-database-automated-backups.md)
