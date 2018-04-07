---
title: Hantera historisk data i Temporala tabeller med bevarandeprincip | Microsoft Docs
description: Lär dig använda temporal bevarandeprincip för att hålla historiska data under din kontroll.
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 04/01/2018
ms.author: bonova
ms.openlocfilehash: 3175236306f05831a78ae8ca01911d0c5d19f893
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Hantera historisk data i Temporala tabeller med bevarandeprincip
Temporala tabeller kan öka databasens storlek mer än vanliga tabeller, särskilt om du behåller historiska data under en längre tidsperiod. Därför är bevarandeprincipen för historiska data en viktig del av planering och hantera livscykeln för alla temporala tabeller. Temporala tabeller i Azure SQL Database har lätt att använda kvarhållning mekanism som hjälper dig att utföra den här uppgiften.

Temporala historiktabeller kvarhållning kan vara konfigurerad på tabell-nivå, som låter användarna skapa flexibla åldern principer. Tillämpa temporal kvarhållning är enkel: det krävs endast en parameter måste anges vid tabell skapas eller schemat ändras.

När du har definierat bevarandeprincip startar Azure SQL Database regelbundet kontrollera om det inte finns historiska rader som är tillgängliga för automatisk Datarensning. Identifiering av matchande rader och deras borttagning från historiktabellen uppstå transparent, bakgrundsaktivitet som är schemalagda och köra av systemet. Ålder villkor för historik tabellraderna kontrolleras baserat på kolumnen som motsvarar slutet av SYSTEM_TIME-perioden. Om loggperioden, till exempel har angetts till sex månader uppfylla tabellraderna berättigad för rensning följande villkor:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

I föregående exempel, antas vi som **ValidTo** kolumnen motsvarar slutet av SYSTEM_TIME-perioden.

## <a name="how-to-configure-retention-policy"></a>Så här konfigurerar du bevarandeprincip?
Innan du konfigurerar bevarandeprincip för en temporal tabell, kontrollera först om temporal historiska kvarhållning har aktiverats *på databasnivå*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Databasen flaggan **is_temporal_history_retention_enabled** är inställt på ON som standard, men användaren kan ändra med ALTER DATABASE-instruktionen. Anges också automatiskt till OFF när [återställning vid tidpunkt](sql-database-recovery-using-backups.md) igen. Om du vill aktivera rensning av temporala historiktabeller kvarhållning för din databas, kör du följande sats:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Du kan konfigurera kvarhållning för temporala tabeller, även om **is_temporal_history_retention_enabled** är AVSTÄNGD, men aktiveras automatisk rensning för föråldrade rader inte i så fall.
> 
> 

Bevarandeprincip konfigureras under skapande av tabell genom att ange värdet för parametern HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Azure SQL-databas kan du ange kvarhållningsperiod med olika tidsenheter: dagar, veckor, månader och år. Om HISTORY_RETENTION_PERIOD utelämnas antas oändlig kvarhållning. Du kan också använda oändlig nyckelordet explicit.

I vissa fall kanske du vill konfigurera kvarhållning när tabellen skulle skapas eller om du vill ändra tidigare konfigurerade värdet. I så fall använder du ALTER TABLE-instruktion:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> Ställa in SYSTEM_VERSIONING på OFF *bevaras inte* kvarhållning periodvärde. Ställa in SYSTEM_VERSIONING på ON utan HISTORY_RETENTION_PERIOD anges explicit resultat i obegränsad kvarhållningsperiod.
> 
> 

Om du vill granska aktuella tillstånd bevarandeprincipen, Använd följande fråga som ansluter till temporala kvarhållning aktivering flaggan på databasnivå med kvarhållningsperioder för enskilda tabeller:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


## <a name="how-sql-database-deletes-aged-rows"></a>Hur SQL-databas tar bort föråldrade rader?
Rensningen beror på layouten index för historiktabellen. Det är viktigt att Observera att *bara historik tabeller med ett grupperat index (B-trädet eller columnstore) kan ha ändlig bevarandeprincip som konfigurerats*. En bakgrundsaktivitet skapas för att rensa föråldrade data för alla temporala tabeller med begränsad Bevarandeperiod.
Rensa logik för det grupperade indexet rowstore (B-trädet) tar bort föråldrade rad i mindre segment (upp till 10 K) minska trycket på databasloggen och i/o-undersystem. Även om rensning logik använder krävs B-trädindex ordning borttagningar för rader som är äldre än bevarandeperioden inte kan garanteras ordentligt. Därför *gör inte alla beroende på Rensa ordning i dina program*.

Uppgiften för grupperade columnstore tar bort hela [rad grupper](https://msdn.microsoft.com/library/gg492088.aspx) samtidigt (vanligtvis innehålla 1 miljon rader varje), vilket är mycket effektivt, särskilt när historiska data genereras i en hög takt.

![Det grupperade columnstore kvarhållning](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Utmärkt datakomprimering och effektiv kvarhållning Rensa gör klustrade kolumnlagringsindexet ett perfekt val för scenarier när din arbetsbelastning genererar snabbt stora mängden historiska data. Mönstret är typiskt för intensiva [transaktionell bearbetning arbetsbelastningar som använder temporala tabeller](https://msdn.microsoft.com/library/mt631669.aspx) för ändringsspårning och granskning, trendanalys eller IoT datapåfyllning.

## <a name="index-considerations"></a>Index-överväganden
Uppgiften för tabeller med rowstore grupperat index kräver index börja med kolumnen som motsvarar slutet av SYSTEM_TIME-perioden. Om index inte finns kan du konfigurera en begränsad period:

*Meddelande 13765, nivå 16, tillstånd 1 <br> </br> ändligt kvarhållningsperiod gick inte att ange på temporal systemversionstabell temporalstagetestdb.dbo.WebsiteUserInfo eftersom historiktabellen ' temporalstagetestdb.dbo.WebsiteUserInfoHistory' innehåller inte nödvändiga grupperat index. Överväg att skapa ett grupperat columnstore- eller B-trädet index som börjar med kolumnen som motsvarar slutet av SYSTEM_TIME period på historiktabellen.*

Det är viktigt att Observera att standard historiktabellen som skapats av Azure SQL Database redan har grupperat index som är godkända för bevarandeprincip. Om du försöker ta bort indexet för en tabell med begränsad Bevarandeperiod misslyckas med följande fel:

*Meddelande 13766, nivå 16, tillstånd 1 <br> </br> går inte att släppa det grupperade indexet 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' eftersom den används för automatisk rensning av föråldrade data. Överväg att inställningen HISTORY_RETENTION_PERIOD oändlig på den motsvarande temporal systemversionstabellen om du vill att släppa indexet.*

Rensa på grupperade columnstore-indexet fungerar optimalt om historiska rader infogas i stigande ordning (i slutet av periodkolumnen), som alltid är fallet när historiktabellen fylls exklusivt av mekanismen för SYSTEM_VERSIONIOING. Om rader i den tidigare tabellen inte sorteras efter slutet av periodkolumnen (vilket kan vara fallet om du har migrerat befintliga historiska data), bör du återskapa grupperade columnstore-indexet ovanpå B-trädet rowstore-index korrekt beställda att uppnå bästa prestanda.

Undvik återskapa grupperat columnstore-index för historiktabellen med begränsat Bevarandeperiod eftersom det kan ändra ordning i radgrupper naturligt införts av systemversionshanteringen igen. Om du behöver återskapa grupperade columnstore-index för historiktabellen gör du genom att återskapa ovanpå kompatibla B-trädindex, bevarar sortering i rowgroups krävs för att rensa vanliga data. Samma metod ska vidtas om du skapar temporal tabell med befintliga historiktabellen som innehåller grupperat kolumnindex utan garanterad dataordning:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

När ändligt kvarhållningsperiod är konfigurerad för historiktabellen med grupperade columnstore-indexet, kan du inte skapa ytterligare icke-grupperade B-trädindex på tabellen:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Ett försök att köra ovan instruktionen misslyckas med följande fel:

*Meddelande 13772, nivå 16, tillstånd 1 <br> </br> kan inte skapa icke-grupperat index i en temporal historiktabell 'WebsiteUserInfoHistory' eftersom den har begränsad Bevarandeperiod och grupperat columnstore-index som definierats.*

## <a name="querying-tables-with-retention-policy"></a>Skicka frågor till tabeller med bevarandeprincip
Alla frågor i den temporala tabellen automatiskt filtrera ut historiska rader som matchar ändlig bevarandeprincip att undvika oväntade och inkonsekventa resultat, eftersom föråldrade rader kan tas bort av rensningsuppgiften i *när som helst i tid och i valfri ordning*.

Följande bild visar frågeplan för en enkel fråga:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

Frågeplanen innehåller ytterligare filter som används till slutet av periodkolumnen (ValidTo) i operatorn grupperat Index skanna för historiktabellen (markerat). Det här exemplet förutsätter att en månad kvarhållningsperioden har angetts för WebsiteUserInfo tabellen.

![Kvarhållning frågefilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Om du frågar historiktabellen direkt kan du se rader som är äldre än angivna period, men utan några garanti för repeterbara frågeresultat. Följande bild visar körning av frågeplan för frågan på historiktabellen utan ytterligare filter:

![Frågar historik utan bevarande filtret](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Förlita dig inte affärslogik på läsa historiktabellen utöver kvarhållningsperiod som du kan få inkonsekventa eller oväntade resultat. Vi rekommenderar att du använder temporala frågor med FOR SYSTEM_TIME-satsen för att analysera data i temporala tabeller.

## <a name="point-in-time-restore-considerations"></a>Peka i tid överväganden för återställning
När du skapar en ny databas genom att [återställa befintlig databas till en specifik tidpunkt](sql-database-recovery-using-backups.md), har temporal kvarhållning inaktiveras när databasnivå. (**is_temporal_history_retention_enabled** -flaggan inställd på OFF). Den här funktionen kan du undersöka alla historiska rader vid en återställning, utan att bekymra dig att föråldrade rader har tagits bort innan du kommer att fråga dem. Du kan använda den *inspektera historiska data utöver konfigurerade bevarandeperioden*.

Anta att en temporal tabell har en månad kvarhållning tidsperioden. Om din databas har skapats i Premium tjänstnivå, skulle du kunna skapa databaskopian med databastillståndet upp till 35 dagar bakåt i tiden. Som effektivt skulle kan du analysera historiska rader som är upp till 65 dagar genom att fråga historiktabellen direkt.

Om du vill aktivera temporal kvarhållning Rensa kör du följande Transact-SQL-instruktionen efter tidpunkt för återställning:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>Nästa steg
Om du vill veta hur du använder Temporala tabeller i dina program, kolla [komma igång med Temporala tabeller i Azure SQL Database](sql-database-temporal-tables.md).

Besök Channel 9 hör en [verkliga kunden temporal implementering fungerat](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [live temporal demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Detaljerad information om Temporala tabeller, granska [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn935015.aspx).

