---
title: Återställa en databas i en SaaS-app med flera innehavare
description: Lär dig hur du återställer en enskild klients SQL-databas efter att data av misstag har tagits bort
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818533"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Återställa en enskild klient med ett SaaS-databas per klient-program

Databas-per-klient-modellen gör det enkelt att återställa en enskild klient till en tidigare tidpunkt utan att påverka andra klienter.

I den här självstudien lär du dig två dataåterställningsmönster:

> [!div class="checklist"]
> * Återställa en databas till en parallell databas (sida vid sida).
> * Återställ en databas på plats och ersätt den befintliga databasen.

|||
|:--|:--|
| Återställa till en parallell databas | Det här mönstret kan användas för uppgifter som granskning, granskning och efterlevnad så att en klient kan granska sina data från en tidigare punkt. Klientens nuvarande databas förblir online och oförändrad. |
| Återställ på plats | Det här mönstret används vanligtvis för att återställa en klient till en tidigare punkt, efter att en klient av misstag tar bort eller skadat data. Den ursprungliga databasen tas bort från linjen och ersätts med den återställda databasen. |
|||

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip SaaS-appen distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip SaaS-programmet](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introduktion till SaaS-klientåterställningsmönster

Det finns två enkla mönster för att återställa en enskild klients data. Eftersom klientdatabaser är isolerade från varandra har återställning av en klient ingen inverkan på någon annan klients data. Azure SQL Database point-in-time-restore (PITR) används i båda mönstren. PITR skapar alltid en ny databas.

* **Återställ parallellt**: I det första mönstret skapas en ny parallell databas tillsammans med klientens aktuella databas. Klienten får sedan skrivskyddad åtkomst till den återställda databasen. Återställda data kan granskas och eventuellt användas för att skriva över aktuella datavärden. Det är upp till appdesignern att avgöra hur klienten kommer åt den återställda databasen och vilka alternativ för återställning som tillhandahålls. Att bara tillåta klienten att granska sina data vid ett tidigare tillfälle kan vara allt som krävs i vissa scenarier.

* **Återställ på plats**: Det andra mönstret är användbart om data har gått förlorade eller skadats och klienten vill återgå till en tidigare punkt. Klienten tas bort från linjen medan databasen återställs. Den ursprungliga databasen tas bort och den återställda databasen har bytt namn. Den ursprungliga databasens säkerhetskopieringskedja är fortfarande tillgänglig efter borttagningen, så du kan återställa databasen till en tidigare tidpunkt om det behövs.

Om databasen använder [aktiv geo-replikering](sql-database-active-geo-replication.md) och återställning parallellt rekommenderar vi att du kopierar alla nödvändiga data från den återställda kopian till den ursprungliga databasen. Om du ersätter den ursprungliga databasen med den återställda databasen måste du konfigurera om och synkronisera om georeplikering.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskripten För Wingtip Tickets SaaS-databas per klient

Wingtip Biljetter SaaS Multitenant Database skript och program källkod finns i [WingtipTicketsSSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Steg för att hämta och häva blockeringen av Wingtip Tickets SaaS-skript finns i den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Innan du börjar

När en databas skapas kan det ta 10 till 15 minuter innan den första fullständiga säkerhetskopian är tillgänglig att återställa från. Om du just har installerat programmet kan du behöva vänta några minuter innan du provar det här scenariot.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulera en klient som av misstag tar bort data

För att demonstrera dessa återställningsscenarier, först "av misstag" ta bort en händelse i en av klientdatabaserna. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Öppna appen Händelser för att granska aktuella händelser

1. Öppna händelsehubbenhttp://events.wtp.&lt(&gt;;användare .trafficmanager.net) och välj **Contoso Concert Hall**.

   ![Hub för evenemang](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Bläddra i listan över händelser och anteckna den sista händelsen i listan.

   ![Senaste händelse visas](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Oavsiktligt" ta bort den senaste händelsen

1. I PowerShell ISE, öppna ... \\Utbildningsmoduler\\Affärskontinuitet och\\återställning av katastrofåterställningTenant\\*Demo-RestoreTenant.ps1*och ange följande värde:

   * **$DemoScenario****1**, *Ta bort senaste händelse (utan biljettförsäljning).* = 
2. Tryck på F5 för att köra skriptet och ta bort den sista händelsen. Följande bekräftelsemeddelande visas:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Sidan Contoso-händelser öppnas. Bläddra nedåt och kontrollera att händelsen är borta. Om händelsen fortfarande finns i listan väljer du **Uppdatera** och kontrollerar att den är borta.
   ![Senaste händelse borttagen](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Återställa en klientdatabas parallellt med produktionsdatabasen

Den här övningen återställer Contoso Concert Hall-databasen till en tidpunkt innan händelsen togs bort. Det här scenariot förutsätter att du vill granska borttagna data i en parallell databas.

 Skriptet *Restore-TenantInParallel.ps1* skapar en parallell klientdatabas med namnet *ContosoConcertHall\_gammal*, med en parallell katalogpost. Det här återställningsmönstret passar bäst för återställning från en mindre dataförlust. Du kan också använda det här mönstret om du behöver granska data för efterlevnad eller granskning. Det är den rekommenderade metoden när du använder [aktiv geo-replikering](sql-database-active-geo-replication.md).

1. Slutför avsnittet [Simulera en klient som av misstag tar bort data.](#simulate-a-tenant-accidentally-deleting-data)
2. I PowerShell ISE, öppna ... \\Utbildningsmoduler\\Affärskontinuitet och\\återställning av katastrofåterställningTenant\\_Demo-RestoreTenant.ps1_.
3. Ange **$DemoScenario** = **2**, Återställ *klienten parallellt*.
4. Om du vill köra skriptet trycker du på F5.

Skriptet återställer klientdatabasen till en tidpunkt innan du tog bort händelsen. Databasen återställs till en ny databas med namnet _ContosoConcertHall\_old_. Katalogmetadata som finns i den här återställda databasen tas bort och sedan läggs databasen till i katalogen med hjälp av en nyckel som skapats från *det gamla\_ContosoConcertHall-namnet.*

Demoskriptet öppnar händelsesidan för den nya klientdatabasen i webbläsaren. Observera från ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` url:en att den här sidan visar data från den återställda databasen där *_old* läggs till i namnet.

Bläddra bland händelserna i webbläsaren för att bekräfta att händelsen som togs bort i föregående avsnitt har återställts.

Att exponera den återställda klienten som en ytterligare klient, med en egen händelseapp, är osannolikt hur du ger en klientåtkomst till återställda data. Det tjänar till att illustrera återställningsmönstret. Vanligtvis ger du skrivskyddad åtkomst till gamla data och behåller den återställda databasen under en definierad period. I exemplet kan du ta bort den återställda klientposten när du är klar med att köra _scenariot Ta bort återställd klient._

1. Ange **$DemoScenario** = **4**, Ta bort *återställd klient .*
2. Om du vill köra skriptet trycker du på F5.
3. Den *gamla posten\_ContosoConcertHall* tas nu bort från katalogen. Stäng händelsesidan för den här klienten i webbläsaren.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Återställa en klient på plats och ersätta den befintliga klientdatabasen

Den här övningen återställer Contoso Concert Hall-klienten till en punkt innan händelsen togs bort. Skriptet *Restore-TenantInPlace* återställer en klientdatabas till en ny databas och tar bort originalet. Det här återställningsmönstret är bäst lämpat för återställning från allvarlig dataskada, och klienten kan behöva hantera betydande dataförlust.

1. Öppna filen **Demo-RestoreTenant.ps1** i PowerShell ISE.
2. Ange **$DemoScenario** = **5**, *Återställ klienten på plats*.
3. Om du vill köra skriptet trycker du på F5.

Skriptet återställer klientdatabasen till en punkt innan händelsen togs bort. Det tar först Contoso Concert Hall hyresgästen off line för att förhindra ytterligare uppdateringar. Sedan skapas en parallell databas genom att återställa från återställningspunkten. Den återställda databasen namnges med en tidsstämpel för att se till att databasnamnet inte står i konflikt med det befintliga klientdatabasnamnet. Därefter tas den gamla klientdatabasen bort och den återställda databasen har bytt namn till det ursprungliga databasnamnet. Slutligen är Contoso Concert Hall förs online så att appen tillgång till den återställda databasen.

Du har återställt databasen till en tidpunkt innan händelsen togs bort. När sidan **Händelser** öppnas bekräftar du att den senaste händelsen har återställts.

När du har återställt databasen tar det ytterligare 10 till 15 minuter innan den första fullständiga säkerhetskopian är tillgänglig för återställning från igen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Återställa en databas till en parallell databas (sida vid sida).
> * Återställa en databas på plats.

Prova självstudien [hantera klientdatabasens schema.](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md)
* [Lär dig mer om säkerhetskopiering av SQL Database](sql-database-automated-backups.md)
