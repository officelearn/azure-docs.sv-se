---
title: Återställa en Azure SQL-databas i en multitenant SaaS-app | Microsoft Docs
description: Lär dig hur du återställer en enda klient SQL-databas när du av misstag tar bort data
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
ms.date: 12/04/2018
ms.openlocfilehash: 4059b0f979e7e6856905f1759129167d62d7b5f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326366"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Återställ en enskild klientorganisation med ett databas-per-klient SaaS-program

Modellen databas-per-klient gör det enkelt att återställa en enskild klient till en tidigare tidpunkt utan att påverka andra klienter.

I de här självstudierna lär du dig två mönster för återställning av data:

> [!div class="checklist"]
> * Återställa en databas till en parallell databasen (sida vid sida).
> * Återställa en databas på plats, ersätter den befintliga databasen.

|||
|:--|:--|
| Återställa till en parallell databasen | Det här mönstret kan användas för uppgifter, t.ex granskning, granskning och efterlevnad så att en klient för att granska deras data från en tidigare tidpunkt. Klientens aktuella databasen är online och oförändrade. |
| Återställ på plats | Det här mönstret används vanligtvis för att återställa en klient till en tidigare tidpunkt när en klient av misstag tar bort eller skadar data. Den ursprungliga databasen tas offline och ersatts med den återställda databasen. |
|||

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip SaaS-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introduktion till SaaS-mönstren klient återställning

Det finns två enkla mönster för att återställa en enskild klientorganisation data. Eftersom klientdatabaser är isolerade från varandra, har återställa en klient ingen inverkan på alla andra klientdata. Funktionen Azure SQL Database punkt i tiden återställning (PITR) används i både mönster. PITR skapar alltid en ny databas.

* **Återställa parallellt**: I det första mönstret skapas en ny parallell databas tillsammans med klientens aktuella databasen. Klienten får sedan skrivskyddad åtkomst till den återställda databasen. Dessa data kan granskas och potentiellt används för att skriva över den aktuella datavärden. Det är upp till appdesignern att avgöra hur klienten har åtkomst till den återställda databasen och vilka alternativ för återställning tillhandahålls. Helt enkelt låta klienten att granska data med en tidigare tidpunkt kan vara allt som krävs i vissa scenarier.

* **Återställer på plats**: Det andra mönstret är användbart om data skulle tappas bort eller skadad och klienten vill återgå till en tidigare tidpunkt. Klienten har tagits offline när databasen har återställts. Den ursprungliga databasen tas bort och den återställda databasen har bytt namn. Säkerhetskopiering kedjan av den ursprungliga databasen är fortfarande tillgängliga efter borttagningen, så att du kan återställa databasen till en tidigare tidpunkt, om det behövs.

Om databasen använder [aktiv geo-replikering](sql-database-active-geo-replication.md) och återställa parallellt, rekommenderar vi att du kopierar alla nödvändiga data från den återställda kopian till den ursprungliga databasen. Om du ersätter den ursprungliga databasen med den återställda databasen måste du konfigurera om och omsynkronisera geo-replikering.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip biljetter SaaS databas-per-klient programskript

Wingtip biljetter SaaS Multitenant databasen skripten och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Anvisningar om att ladda ned och avblockera Wingtip biljetter SaaS-skript finns i den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Innan du börjar

När en databas har skapats, kan det ta 10 – 15 minuter innan den första fullständiga säkerhetskopian är tillgänglig för att återställa från. Om du precis har installerat programmet kan du behöva vänta några minuter innan du försöker att det här scenariot.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulera en klient av misstag tar bort data

För att demonstrera hur dessa scenarion för återställning, först ”av misstag” ta bort en händelse i någon av klientdatabaser. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Öppna appen händelser för att granska de aktuella händelserna

1. Öppna Evenemangshubben (http://events.wtp.&lt; användare&gt;. trafficmanager.net), och välj **Contosos Konserthall**.

   ![Events Hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Bläddra i listan över händelser och anteckna den sista händelsen i listan.

   ![Senaste händelse visas](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>”Råkar” ta bort den sista händelsen

1. I PowerShell ISE öppnar du... \\Inlärningsmoduler\\affärskontinuitet och Haveriberedskap\\RestoreTenant\\*Demo-RestoreTenant.ps1*, och ange följande värde:

   * **$DemoScenario** = **1**, *ta bort senaste händelse (med inga biljettförsäljningar)*.
2. Tryck på F5 för att köra skriptet och ta bort den sista händelsen. Följande bekräftelsemeddelande visas:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Contoso händelser sida öppnas. Bläddra nedåt och kontrollera att händelsen är borta. Om händelsen är fortfarande i listan, Välj **uppdatera** och kontrollera att det har gått.
   ![Senaste händelsen har tagits bort](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Återställa en klientdatabas parallellt med produktionsdatabasen

Den här övningen återställer Contosos Konserthall databasen till en tidpunkt innan händelsen togs bort. Det här scenariot förutsätter att du vill granska de borttagna data i en parallell-databas.

 Den *återställning TenantInParallel.ps1* skriptet skapar en parallell klientdatabas med namnet *ContosoConcertHall\_gamla*, med en parallell katalogpost. Det här mönstret av återställning passar bäst för att återställa från en mindre data går förlorade. Du kan också använda det här mönstret om du vill granska data för efterlevnad eller granskning. Det är den rekommenderade metoden när du använder [aktiv geo-replikering](sql-database-active-geo-replication.md).

1. Slutför den [simulera en klient oavsiktligen Raderar data](#simulate-a-tenant-accidentally-deleting-data) avsnittet.
2. I PowerShell ISE öppnar du... \\Inlärningsmoduler\\affärskontinuitet och Haveriberedskap\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
3. Ange **$DemoScenario** = **2**, *återställning klient parallellt*.
4. Tryck på F5 för att köra skriptet.

Skriptet återställer klientdatabasen till en tidpunkt innan du tog bort händelsen. Databasen har återställts till en ny databas med namnet _ContosoConcertHall\_gamla_. Katalogen metadata som finns i den här återställda databasen tas bort och sedan databasen läggs till i katalogen med hjälp av en nyckel som skapas från den *ContosoConcertHall\_gamla* namn.

Demo-skriptet öppnas sidan händelser för den här nya klientdatabas i webbläsaren. Obs från URL: en ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` att den här sidan visar data från den återställda databasen där *_old* läggs till i namnet.

Rulla händelserna som anges i webbläsaren så att händelsen har tagits bort i föregående avsnitt har återställts.

Exponera återställda klienten som ett ytterligare klienten, med sin egen evenemangsapp är inte troligt att hur du ger en klient-åtkomst till återställda data. Den används för att illustrera mönstret återställning. Normalt ge läsbehörighet till den gamla data och behålla den återställda databasen under en definierad period. I det här exemplet du ta bort posten återställda klient när du är klar genom att köra den _Remove återställts klient_ scenario.

1. Ange **$DemoScenario** = **4**, *Remove återställts klient*.
2. Tryck på F5 för att köra skriptet.
3. Den *ContosoConcertHall\_gamla* nu bort från katalogen. Stäng sidan händelser för den här klienten i din webbläsare.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Återställa en klient på plats, ersätter den befintliga klientdatabasen

Den här övningen återställer Contosos Konserthall-klient till en tidpunkt innan händelsen togs bort. Den *återställning TenantInPlace* skriptet återställer en klientdatabas till en ny databas och tar bort ursprungligt. Det här mönstret för återställning är bäst att återställa från skadade allvarligt data och klienten kan behöva hantera dataförluster.

1. I PowerShell ISE öppnar den **Demo-RestoreTenant.ps1** fil.
2. Ange **$DemoScenario** = **5**, *Restore-klient på plats*.
3. Tryck på F5 för att köra skriptet.

Skriptet återställer klientdatabasen till en tidpunkt innan händelsen togs bort. Det tar först Contosos Konserthall klienten offline förhindrar ytterligare uppdateringar. Sedan skapas en parallell databas genom att återställa från återställningspunkten. Den återställda databasen heter med en tidsstämpel för att kontrollera att namnet på databasen inte står i konflikt med det befintliga databasnamnet för klienten. Därefter gamla klientdatabasen tas bort och den återställda databasen har bytt namn till det ursprungliga databasnamnet. Slutligen är Contosos Konserthall online så att appen åtkomst till den återställda databasen.

Du återställt har databasen till en tidpunkt innan händelsen togs bort. När den **händelser** öppnas, bekräfta att den senaste händelsen har återställts.

När du har återställt databasen tar en annan 10 till 15 minuter innan den första fullständiga säkerhetskopian är tillgängligt för att återställa från igen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Återställa en databas till en parallell databasen (sida vid sida).
> * Återställa en databas på plats.

Prova den [hantera klient databasschemat](saas-tenancy-schema-management.md) självstudien.

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md)
* [Lär dig mer om SQL Database-säkerhetskopior](sql-database-automated-backups.md)
