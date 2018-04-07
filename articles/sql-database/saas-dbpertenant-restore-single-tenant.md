---
title: Återställa en Azure SQL-databas i en multitenant SaaS-app | Microsoft Docs
description: Lär dig hur du återställer en enskild klient SQL-databas när data har tagits bort av misstag
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
ms.openlocfilehash: eb7e87934269a5e1ba453e20f6f409a10dfbda5b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Återställa en enskild klient till en databas per klient SaaS-program

Databasen per klient modellen gör det enkelt att återställa en enskild klient till en tidigare tidpunkt utan att påverka andra klienter.

I kursen får du lära dig två mönster för återställning av data:

> [!div class="checklist"]

> * Återställa en databas till en parallell databasen (sida vid sida).
> * Återställa en databas på plats, ersätter den befintliga databasen.


|||
|:--|:--|
| Återställa till en parallell databasen | Det här mönstret kan användas för åtgärder som granskning, granskning och kompatibilitet så att en klient att inspektera sina data från ett tidigare tillstånd. Klientens aktuella databasen är online och oförändrade. |
| Återställa på plats | Det här mönstret är vanligt att återställa en klient till en tidigare, när en klient tar bort av misstag eller skadar data. Den ursprungliga databasen tas offline och ersätts med den återställda databasen. |
|||

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip SaaS-appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introduktion till SaaS-klient återställning mönster

Det finns två enkla mönster för att återställa en enskild klientorganisation data. Eftersom klienten databaser är isolerade från varandra, har återställa en klient ingen inverkan på data för alla andra innehavare. Funktionen Azure SQL Database punkt i tiden återställning (PITR) används i båda mönstren. PITR skapas alltid en ny databas.   

* **Återställa parallellt**: I det första mönstret skapas en ny databas för parallell tillsammans med klientens aktuella databasen. Klienten får sedan skrivskyddad åtkomst till den återställda databasen. Dessa data kan granskas och potentiellt används för att skriva över den aktuella värden. Är det upp Designer appen att fastställa hur klienten har åtkomst till den återställda databasen och vilka alternativ för återställning. Bara låta klienten granska sina data vid ett tidigare kanske allt som krävs i vissa scenarier. 

* **Återställ plats**: det andra mönstret är användbart om data har förlorats eller skadad och klienten vill återgå till en tidigare. Klienten har tagits offline när databasen har återställts. Den ursprungliga databasen tas bort och byta namn på den återställda databasen. Säkerhetskopiering kedja av den ursprungliga databasen förblir tillgängligt efter borttagningen, så att du kan återställa databasen till en tidigare tidpunkt, om det behövs.

Om databasen använder [georeplikering](sql-database-geo-replication-overview.md) och återställa parallellt, rekommenderar vi att du kopierar alla nödvändiga data från den återställda kopian i den ursprungliga databasen. Om du ersätta den ursprungliga databasen med den återställda databasen måste du konfigurera om och omsynkronisera geo-replikering.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas per klient

Wingtip biljetter SaaS Multitenant databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript, finns det [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Innan du börjar

När en databas har skapats kan det ta 10 – 15 minuter innan den första kompletta säkerhetskopian är tillgängligt för att återställa från. Om du precis har installerat programmet kan du behöva vänta några minuter innan du försöker det här scenariot.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulera en klient oavsiktlig borttagning av data

För att visa dessa scenarion för återställning ”av misstag” ta bort en händelse i databaserna för innehavare. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Öppna appen händelser om du vill granska aktuella händelser

1. Öppna händelser navet (http://events.wtp.&lt; användaren&gt;. trafficmanager.net), och välj **Contoso samklang Hall**.

   ![Händelser Hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Bläddra i listan över händelser och anteckna den sista händelsen i listan.

   ![Senaste händelse visas](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>”Råkar” ta bort den sista händelsen

1. I PowerShell ISE öppna... \\Learning moduler\\affärskontinuitet och Haveriberedskap\\RestoreTenant\\*Demo-RestoreTenant.ps1*, och anger följande värde:

   * **$DemoScenario** = **1**, *ta bort senaste händelse (med inga Biljettförsäljning)*.
2. Tryck på F5 för att köra skriptet och ta bort den sista händelsen. Följande bekräftelsemeddelande visas:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Contoso händelser sidan öppnas. Bläddra nedåt och kontrollera att händelsen är borta. Om händelsen är fortfarande i listan, väljer **uppdatera** och kontrollera att den är.

   ![Senaste händelse tas bort](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Återställa en databas klient parallellt med produktionsdatabasen

Den här övningen återställer Contoso samklang Hall databasen till en tidpunkt innan händelsen har tagits bort. Det här scenariot förutsätter att du vill granska de borttagna data i en parallell databas.

 Den *återställning TenantInParallel.ps1* skriptet skapar en parallell klient-databas med namnet *ContosoConcertHall\_gamla*, med en parallell katalogpost. Det här mönstret av återställning passar bäst för att återställa från en mindre data går förlorade. Du kan också använda det här mönstret om du behöver granska data för efterlevnad och granskning. Det är den rekommenderade metoden när du använder [georeplikering](sql-database-geo-replication-overview.md).

1. Slutför den [simulera en klient oavsiktlig borttagning av data](#simulate-a-tenant-accidentally-deleting-data) avsnitt.
2. I PowerShell ISE öppna... \\Learning moduler\\affärskontinuitet och Haveriberedskap\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
3. Ange **$DemoScenario** = **2**, *återställning klient parallellt*.
4. Tryck på F5 för att köra skriptet.

Skriptet återställer databasen för klient till en tidpunkt innan du tar bort händelsen. Databasen har återställts till en ny databas med namnet _ContosoConcertHall\_gamla_. Katalogen metadata som finns i den här återställda databasen tas bort och sedan databasen har lagts till i katalogen med hjälp av en nyckel som skapas från den *ContosoConcertHall\_gamla* namn.

Demo-skriptet öppnas sidan händelser för den nya innehavare databasen i din webbläsare. Anteckning från URL: en ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` att den här sidan visar data från den återställda databasen där *_old* har lagts till i namnet.

Rulla de händelser som visas i webbläsaren om du vill bekräfta att händelsen tas bort i föregående avsnitt har återställts.

Exponera återställda innehavaren som en ytterligare klient med egna händelser app är sannolikt inte hur du ger en klient åtkomst till data som återställs. Den används för att illustrera restore-mönster. Normalt ge läsbehörighet till den gamla data och bevara den återställda databasen för en definierad period. I det här exemplet du kan ta bort posten återställda klient när du är klar genom att köra den _ta bort återställts klient_ scenario.

1. Ange **$DemoScenario** = **4**, *ta bort återställts klient*.
2. Tryck på F5 för att köra skriptet.
3. Den *ContosoConcertHall\_gamla* post nu tagits bort från katalogen. Stäng sidan händelser för den här klienten i webbläsaren.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Återställa en klient på plats, ersätter den befintliga databasen för klient

Den här övningen återställer Contoso samklang Hall-klient till en punkt innan händelsen har tagits bort. Den *återställning TenantInPlace* skript återställer databasen för en klient till en ny databas och tar bort ursprungligt. Återställ mönstret är bäst att återställa från allvarliga skador och klienten kan behöva anpassa dataförluster.

1. I PowerShell ISE öppnar den **Demo-RestoreTenant.ps1** fil.
2. Ange **$DemoScenario** = **5**, *återställning klient på plats*.
3. Tryck på F5 för att köra skriptet.

Skriptet återställer databasen för klient till en punkt innan händelsen har tagits bort. Det tar först Contoso samklang Hall innehavaren offline att förhindra framtida uppdateringar. Sedan skapas en parallell databas genom att återställa från återställningspunkten. Den återställda databasen heter med en tidsstämpel för att kontrollera databasens namn inte ligger i konflikt med befintliga klient-databasnamn. Sedan den gamla klient databasen tas bort och byta namn på den återställda databasen till det ursprungliga databasnamnet. Slutligen är Contoso samklang Hall online så att appen åtkomst till den återställda databasen.

Du återställt har databasen till en tidpunkt innan händelsen har tagits bort. När den **händelser** öppnas, bekräfta att den sista händelsen har återställts.

När du har återställt databasen tar en annan 10 – 15 minuter innan den första kompletta säkerhetskopian är tillgängligt för att återställa från igen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Återställa en databas till en parallell databasen (sida vid sida).
> * Återställa en databas på plats.

Försök i [hantera klient-databasschemat](saas-tenancy-schema-management.md) kursen.

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md)
* [Lär dig mer om säkerhetskopiering av SQL-databaser](sql-database-automated-backups.md)
