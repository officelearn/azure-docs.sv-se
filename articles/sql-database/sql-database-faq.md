---
title: "Azure SQL Database vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor kunder fråga om molnet databaser och Azure SQL Database, Microsofts relationella databashanteringssystem (RDBMS) och databasen som en tjänst i molnet."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 59058b3445fdaf75df331d771afd28599491c52e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-faq"></a>Vanliga frågor om SQL Database

## <a name="what-is-the-current-version-of-sql-database"></a>Vad är den aktuella versionen av SQL-databasen?
Den aktuella versionen av SQL-databas är V12. Version V11 har tagits bort.

## <a name="what-is-the-sla-for-sql-database"></a>Vad är SLA för SQL-databasen?
Vi garanterar att kunderna har anslutning mellan sin enkla och elastiska Microsoft Azure SQL-databas på Basic-, Standard- eller Premium-nivå och vår Internetgateway minst 99,99 procent av tiden. Mer information finns i [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Hur jag för att återställa lösenord för serveradministratören?
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL Servers**, välja en server i listan och klicka sedan på **Återställ lösenord**.

## <a name="how-do-i-manage-databases-and-logins"></a>Hur hanterar jag databaser och inloggningar?
Se [hantera databaser och inloggningar](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Hur kan jag vara säker på att endast auktoriserade IP-adresser har tillgång till en server?
Se [så här: konfigurera brandväggsinställningar på SQL-databas](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Hur ska användning av SQL-databas på min faktura?
Växlar SQL-databas på en förutsägbar timvis avgift baserat på båda tjänstnivån + prestandanivån för enskilda databaser eller per elastisk pool-edtu: er. Faktiska användningen beräknas och proportionerligt varje timma, så fakturan kan visa delar av en timme. Om det finns en databas för 12 timmar i månaden, visar fakturan användning av 0,5 dagar. Dessutom har servicenivåer + prestandanivå och edtu: er per pool delats upp i faktura för att göra det lättare att se hur många databasdagar som du använde för varje i en månad.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Vad händer om en enskild databas är aktiv för mindre än en timme eller använder en högre tjänstnivå för mindre än en timme?
Du debiteras för varje timme finns en databas med högsta tjänstnivån + prestandanivå som tillämpas under den timmen, oavsett användning eller om databasen var aktiv för mindre än en timme. Om du skapar en databas och ta bort den fem minuter senare visar fakturan en avgift för en databas timme. 

Exempel:

* Om du skapar en grundläggande databas och sedan omedelbart uppgradera den till Standard S1 debiteras du med Standard S1-hastighet för den första timmen.
* Om du uppgraderar en databas från Basic till Premium klockan 10:00. och att uppgraderingen har slutförts klockan 1:35 dagen debiteras du med Premium-hastighet som börjar vid 1:00 a.m. 
* Om du nedgradera en databas från Premium till Basic klockan 11:00 och den är klar kl. 2:15 sedan databasen debiteras med Premium-hastighet fram till 15:00. Därefter debiteras den med grundläggande priser.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Hur elastisk pool användning visas på min faktura och vad som händer när jag ändrar edtu: er per pool?
Elastisk pool debiterar visa upp på fakturan som elastiska dtu: er (edtu: er) i steg som visas under edtu: er per pool på [prissättningssidan](https://azure.microsoft.com/pricing/details/sql-database/). Det kostar ingenting per databas för elastiska pooler. Du debiteras för varje timme poolen finns på den högsta eDTU, oavsett användning eller om poolen har aktiva för mindre än en timme. 

Exempel:

* Om du skapar en Standard elastisk pool med 200 edtu: er på 11:18:00, debiteras lägger till fem databaser i poolen, du för 200 edtu: er för hela timme början på 11: 00 i resten av dagen.
* På dag 2 i 05:05:00, databasen 1 börjar förbruka 50 edtu: er och innehåller konstant genom dagen. Databaser 2-5 variera mellan 0 och 80 edtu: er. Under dagen, att lägga till fem andra databaser som använder olika edtu: er under dagen. Dag 2 är en hel dag faktureras enligt 200 eDTU. 
* På dag 3, 5 klockan du lägger till en annan 15 databaser. Databasanvändningen ökar under dagen till den punkt där du vill öka edtu: er för poolen från 200 till 400 kl. 8:05 Avgifter på nivån 200 eDTU gällde till 20: 00 och ökar till 400 edtu: er för återstående fyra timmar. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Elastisk pool fakturerings- och information om priser
Elastiska pooler debiteras per följande egenskaper:

* En elastisk pool debiteras när skapades, även om det finns inga databaser i poolen.
* En elastisk pool faktureras timvis. Det här är samma avläsning frekvens som prestandanivåer för enskilda databaser.
* Om en elastisk pool, storleksändras till ett nytt antal edtu: er sedan faktureras poolen inte enligt den nya mängden edtu: er tills storleksändring åtgärden har slutförts. Det följer samma mönster som ändrar prestandanivåerna för enskilda databaser.
* Priset på en elastisk pool baseras på antalet edtu: er i poolen. Priset på en elastisk pool är oberoende av antalet och användning av de elastiska databaserna i den.
* Priset beräknas som (antal pool-edtu: er) x (pris per eDTU).

Enhetspriset för en elastisk pool-eDTU är högre än DTU enhetspriset för en enskild databas i samma tjänstnivån. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 

Edtu: er och tjänsten nivåer finns [SQL Database-alternativ och prestanda](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Hur stöder användning av aktiv geo-replikering i en elastisk pool visas på min faktura?
Till skillnad från enskilda databaser, med hjälp av [aktiv geo-replikering](sql-database-geo-replication-overview.md) med elastiska databaser inte har en direkt inverkan för fakturering.  Endast debiteras du för edtu: er som etableras för varje pooler (primär och sekundär pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Hur påverkar användningen av granskningsfunktionen fakturan?
Granskning är inbyggd i SQL Database-tjänsten utan extra kostnad och är tillgänglig för Basic, Standard och Premium-databaser. Men om du vill lagra granskningsloggarna utifrån granskning funktionen använder ett Azure Storage-konto och priser för tabeller och köer i Azure Storage gäller storleken på din granskningslogg.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Hur hittar rätt tjänstnivå och prestandanivå servicenivåer för enskilda databaser och elastiska pooler?
Det finns några verktyg som är tillgängliga: 

* Lokala databaser, använda den [DTU storlek advisor](http://dtucalculator.azurewebsites.net/) rekommenderar databaser och dtu: er som krävs och utvärdera flera databaser för elastiska pooler.
* Om en enskild databas lönar sig att den finns i en pool, rekommenderar Azures intelligent motorn en elastisk pool om den ser ett mönster för användningshistorik som på grund av den. Se [övervaka och hantera en elastisk pool med Azure-portalen](sql-database-elastic-pool-manage-portal.md). Mer information om hur du gör dina egna själv finns [pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool.md)
* Om du vill ringa en enskild databas uppåt eller nedåt finns [prestandaråd för enskilda databaser](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Hur ofta kan jag ändra servicenivå för nivå eller prestanda för en enskild databas?
Du kan ändra tjänstnivå (mellan Basic, Standard och Premium) eller prestandanivån i en tjänstnivå (till exempel S1 S2) så ofta du vill. Du kan ändra totalt fyra gånger under en 24-timmarsperiod servicenivå nivå eller prestanda för den tidigare versionen databaser.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Hur ofta kan jag justera edtu: er per pool?
Så ofta du vill.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Hur lång tid tar det att ändra servicenivå för nivå eller prestanda för en enskild databas eller flytta en databas till och från en elastisk pool?
Ändra tjänstnivån för en databas och flyttas till och från en pool måste databasen som ska kopieras på plattformen som bakgrunden. Ändra tjänstnivån kan ta några minuter till flera timmar beroende på storleken på databaserna. I båda fallen förblir databaserna online och tillgänglig under flytten. Mer information om hur du ändrar enskilda databaser finns [ändra tjänstnivån för en databas](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>När bör jag använda en enskild databas jämfört med elastiska databaser?
I allmänhet elastiska pooler är utformade för att en typisk [programmönster för programvara som en tjänst (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), där det finns en databas per kund eller klient. Att köpa separata databaser och skaffa stora resurser som kan möta de varierande och de högsta behoven för varje databas är sällan en kostnadseffektiv lösning. Du hanterar den samlade prestandan för poolen med pooler, och databaserna skala upp eller ned automatiskt. Azures intelligent motorn rekommenderar en pool för databaser när en användningsmönstret på grund av den. Mer information finns i [elastisk pool vägledning](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Vad innebär det att ha upp till 200 % av sin högsta etablerade databaslagring för säkerhetskopieringslagring?
Lagring för säkerhetskopiering är lagring som är associerade med dina automatiserade databassäkerhetskopieringar som används för [punkt-i-tid-återställning](sql-database-recovery-using-backups.md#point-in-time-restore) och [geo-återställning](sql-database-recovery-using-backups.md#geo-restore). Microsoft Azure SQL-databas tillhandahåller upp till 200 % av din högsta etablerade databaslagring för lagring av säkerhetskopior utan extra kostnad. Till exempel om du har en Standard DB-instans med en etablerade DB storlek på 250 GB finns med 500 GB lagring för säkerhetskopiering utan extra kostnad. Om databasen överskrider den angivna lagringen för säkerhetskopiering, kan du minska kvarhållningsperioden genom att kontakta Azure-supporten eller betala för extra säkerhetskopieringslagring faktureras enligt standardkostnaden geografiskt Redundant lagring med läsbehörighet (RA-GRS). Mer information om fakturering för RA-GRS finns prisinformation för lagring.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Jag kan flyttas från Web/företag till nya servicenivåer, vad behöver jag veta?
Azure SQL-webb- och affärsdatabaser är nu inaktiverad. Nivåerna Basic, Standard och Premium och elastiska Ersätt retiring webb- och affärsdatabaser. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Vad är en förväntad replikeringsfördröjning vid geo-replikering en databas mellan två regioner inom samma Azure geografi?
Vi stöder för närvarande ett Återställningpunktsmål fem sekunder och replikeringsfördröjning har mindre än att när geo-sekundär finns i Azure bör parad region och på samma tjänstnivån.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Vad är en förväntad replikeringsfördröjning när geo sekundär skapas i samma region som den primära databasen?
Baserat på uteslutande data, finns det inte för mycket skillnaden mellan intra-region och mellan region replikeringsfördröjning när Azure rekommenderar region parad används. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Om det finns ett fel i nätverket mellan två regioner, hur logik för omprövning fungerar när geo-replikering konfigureras?
Om det finns ett kopplas från, gör vi var 10: e sekund för att återupprätta anslutningarna.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Vad gör jag för att säkerställa att viktiga förändringar i den primära databasen replikeras?
Geo-sekundär är en asynkron replikering och vi gör inga försök att behållas i fullständig synkronisering med primärt. Men vi ger en metod för att tvinga synkronisering så replikering av viktiga ändringar (till exempel lösenordsuppdateringar). Framtvingad synkronisering påverkar prestanda eftersom den anropande tråden blockeras tills alla genomförda transaktioner replikeras. Mer information finns i [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Vilka verktyg är tillgängliga för att övervaka replikeringsfördröjning mellan den primära databasen och geo sekundär?
Använda realtid replikeringsfördröjning mellan den primära databasen och geo sekundära via en DMV. Mer information finns i [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Flytta en databas till en annan server i samma prenumeration
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, Välj en databas i listan och klicka sedan på **kopiera**. Se [kopiera en Azure SQL database](sql-database-copy.md) för mer information.

## <a name="to-move-a-database-between-subscriptions"></a>Flytta en databas mellan prenumerationer
I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-servrar** och välj sedan den server som är värd för databasen i listan. Klicka på **flytta**, och välj sedan resurserna som ska flyttas och prenumeration att flytta till.
