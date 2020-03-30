---
title: Hantera historiska data i temporala tabeller
description: Lär dig hur du använder tidsbevarandeprincip för att hålla historiska data under din kontroll.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820693"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Hantera historiska data i temporala tabeller med bevarandeprincip

Temporaltabeller kan öka databasstorleken mer än vanliga tabeller, särskilt om du behåller historiska data under en längre tidsperiod. Bevarandeprincip för historiska data är därför en viktig aspekt av planering och hantering av livscykeln för varje temporaltabell. Temporaltabeller i Azure SQL Database levereras med lättanvänd kvarhållningsmekanism som hjälper dig att utföra den här uppgiften.

Temporal historia kvarhållning kan konfigureras på den enskilda tabellnivå, vilket gör det möjligt för användare att skapa flexibla åldrande poliser. Att tillämpa tidsbevarande är enkelt: det kräver bara en parameter som ska anges när tabellskapande eller schemaändring.

När du har definierat bevarandeprincipen börjar Azure SQL Database kontrollera regelbundet om det finns historiska rader som är kvalificerade för automatisk datarensning. Identifiering av matchande rader och borttagning från historiktabellen sker transparent, i den bakgrundsaktivitet som schemaläggs och körs av systemet. Åldersvillkoret för historiktabellraderna kontrolleras baserat på kolumnen som representerar slutet av SYSTEM_TIME perioden. Om kvarhållningsperioden till exempel är inställd på sex månader uppfyller tabellrader som är berättigade till rensning följande villkor:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

I föregående exempel antog vi att **kolumnen ValidTo** motsvarar slutet av SYSTEM_TIME perioden.

## <a name="how-to-configure-retention-policy"></a>Konfigurera bevarandeprincip

Innan du konfigurerar bevarandeprincip för en temporal tabell kontrollerar du först om tidsmässig historisk kvarhållning är aktiverad *på databasnivå*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Databasflagga **is_temporal_history_retention_enabled** är inställd på PÅ som standard, men användarna kan ändra den med ALTER DATABASE-satsen. Den ställs också automatiskt in på OFF efter [tidsåterställning.](sql-database-recovery-using-backups.md) Om du vill aktivera rensning av tidshistorik för databasen kör du följande sats:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Du kan konfigurera kvarhållning för temporala tabeller även om **is_temporal_history_retention_enabled** är OFF, men automatisk rensning för äldre rader utlöses inte i så fall.

Bevarandeprincipen konfigureras när tabell skapas genom att ange värde för parametern HISTORY_RETENTION_PERIOD:

```sql
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
```

Med Azure SQL Database kan du ange kvarhållningsperiod med hjälp av olika tidsenheter: DAGAR, VECKOR, MÅNADER och ÅR. Om HISTORY_RETENTION_PERIOD utelämnas antas INFINITE-kvarhållning. Du kan också använda infinite-nyckelordet explicit.

I vissa fall kanske du vill konfigurera kvarhållning när du har skapat tabellen eller ändra tidigare konfigurerat värde. I så fall använd ALTER TABLE-satsen:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Om du ställer in SYSTEM_VERSIONING på AV *bevaras inte* värdet för kvarhållningsperiod. Om du ställer in SYSTEM_VERSIONING på PÅ utan HISTORY_RETENTION_PERIOD anges uttryckligen resulterar i oändlig kvarhållningsperiod.

Om du vill granska bevarandeprincipens aktuella tillstånd använder du följande fråga som sammanfogar flaggan för tidsbevarandetion på databasnivå med kvarhållningsperioder för enskilda tabeller:

```sql
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
```


## <a name="how-sql-database-deletes-aged-rows"></a>Så här tar SQL Database bort gamla rader

Rensningsprocessen beror på indexlayouten för historiktabellen. Det är viktigt att notera att *endast historiktabeller med ett klustrade index (B-tree eller columnstore) kan ha begränsad bevarandeprincip konfigurerad*. En bakgrundsaktivitet skapas för att utföra åldrad datarensning för alla temporala tabeller med begränsad kvarhållningsperiod.
Rensningslogiken för det klustrade indexet (Rowstore) (B-träd) tar bort en gammal rad i mindre segment (upp till 10 000) vilket minimerar trycket på databasloggen och IO-delsystemet. Även om rensningslogik använder obligatoriskt B-trädindex, kan ordningen på borttagningar för rader som är äldre än kvarhållningsperioden inte garanteras ordentligt. Därför *inte ta något beroende av rensning ordning i dina program*.

Rensningsuppgiften för den klustrade columnstore tar bort hela [radgrupper](https://msdn.microsoft.com/library/gg492088.aspx) samtidigt (innehåller vanligtvis 1 miljon rader vardera), vilket är mycket effektivt, särskilt när historiska data genereras i hög takt.

![Klusterad bevarande av kolumnaffär](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Utmärkt datakomprimering och effektiv lagringsrensning gör klustrade columnstore-index till ett perfekt val för scenarier när din arbetsbelastning snabbt genererar en stor mängd historiska data. Det mönstret är typiskt för intensiva [transaktionsbearbetningsarbetsbelastningar som använder temporala tabeller](https://msdn.microsoft.com/library/mt631669.aspx) för ändringsspårning och granskning, trendanalys eller IoT-datainmatning.

## <a name="index-considerations"></a>Indexöverväganden

Rensningsuppgiften för tabeller med rowstore-klustrade index kräver index för att starta med kolumnen som motsvarar slutet av SYSTEM_TIME perioden. Om ett sådant index inte finns kan du inte konfigurera en begränsad kvarhållningsperiod:

*Msg 13765, Nivå 16, <br> </br> Stat 1 Inställning finita retention period misslyckades på system-versioned temporal tabell "temporalstagetestdb.dbo.WebsiteUserInfo" eftersom historien tabellen "temporalstagetestdb.dbo.WebsiteUserInfoHistory" inte innehåller krävs klustrade index. Överväg att skapa ett grupperat columnstore- eller B-trädindex som börjar med kolumnen som matchar slutet av SYSTEM_TIME perioden i historiktabellen.*

Det är viktigt att notera att standardhistoriktabellen som skapats av Azure SQL Database redan har ett klustrade index, vilket är kompatibelt för bevarandeprincipen. Om du försöker ta bort indexet i en tabell med begränsad kvarhållningsperiod misslyckas åtgärden med följande fel:

*Msg 13766, Nivå 16, <br> </br> Stat 1 Kan inte släppa det klustrade indexet "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory" eftersom det används för automatisk rensning av äldre data. Överväg att ange HISTORY_RETENTION_PERIOD till INFINITE i motsvarande tidstabell för systemversioner om du behöver släppa det här indexet.*

Rensning på det klustrade columnstore-indexet fungerar optimalt om historiska rader infogas i stigande ordning (ordnad efter slutet av perioden, vilket alltid är fallet när historiktabellen fylls i uteslutande av SYSTEM_VERSIONIOING mekanismen. Om rader i historiktabellen inte beställs efter slutet av perioden kolumn (vilket kan vara fallet om du migrerade befintliga historiska data), bör du återskapa klustrade columnstore index ovanpå B-träd rowstore index som är korrekt beställt, för att uppnå optimala Prestanda.

Undvik att återskapa grupperat columnstore-index i historiktabellen med den finita kvarhållningsperioden, eftersom det kan ändra ordningen i radgrupperna som naturligt införs av systemversionsåtgärden. Om du behöver återskapa grupperat columnstore-index i historiktabellen gör du det genom att återskapa det ovanpå det kompatibla B-trädindexet och bevara ordningen i de radgrupper som krävs för vanlig datarensning. Samma metod bör användas om du skapar temporaltabell med befintlig historiktabell som har grupperat kolumnindex utan garanterad dataordning:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

När finit kvarhållningsperiod har konfigurerats för historiktabellen med det klustrade columnstore-indexet kan du inte skapa ytterligare B-trädindex som inte är grupperade i tabellen:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Ett försök att köra ovanstående programsats misslyckas med följande fel:

*Msg 13772, Nivå 16, <br> </br> Stat 1 Kan inte skapa icke-grupperade index på en temporal historiktabell "WebsiteUserInfoHistory" eftersom den har begränsad kvarhållningsperiod och klustrade columnstore-index har definierats.*

## <a name="querying-tables-with-retention-policy"></a>Fråga tabeller med bevarandeprincip

Alla frågor i den tidsmässiga tabellen filtrerar automatiskt bort historiska rader som matchar begränsad bevarandeprincip, för att undvika oförutsägbara och inkonsekventa resultat, eftersom gamla rader kan tas bort av rensningsaktiviteten, *när som helst och i godtycklig ordning*.

Följande bild visar frågeplanen för en enkel fråga:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Frågeplanen innehåller ytterligare filter som används för att avsluta periodkolumnen (ValidTo) i operatorn Clustered Index Scan i historiktabellen (markerad). Det här exemplet förutsätter att en månad kvarhållningsperiod angavs i tabellen WebbplatsAnvändareInfo.

![Frågefilter för kvarhållning](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Men om du frågar historiktabellen direkt kan du se rader som är äldre än den angivna kvarhållningsperioden, men utan någon garanti för repeterbara frågeresultat. Följande bild visar frågekörningsplanen för frågan i historiktabellen utan att ytterligare filter tillämpas:

![Frågehistorik utan kvarhållningsfilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Lita inte på din affärslogik på att läsa historiktabellen efter kvarhållningsperioden eftersom du kan få inkonsekventa eller oväntade resultat. Vi rekommenderar att du använder tidsmässiga frågor med FOR SYSTEM_TIME-satsen för att analysera data i temporala tabeller.

## <a name="point-in-time-restore-considerations"></a>Tidsåterställning

När du skapar en ny databas genom [att återställa befintlig databas till en viss tidpunkt](sql-database-recovery-using-backups.md)har den tidsmässigt bevarande inaktiverat på databasnivå. **(is_temporal_history_retention_enabled** flagga inställd på OFF). Med den här funktionen kan du undersöka alla historiska rader vid återställning, utan att behöva oroa dig för att gamla rader tas bort innan du får fråga dem. Du kan använda den för att *granska historiska data efter konfigurerad kvarhållningsperiod*.

Säg att en temporal tabell har en månad kvarhållningsperiod angiven. Om databasen skapades på Premium Service-nivån kan du skapa databaskopia med databastillståndet upp till 35 dagar tillbaka i det förflutna. Det skulle effektivt göra att du kan analysera historiska rader som är upp till 65 dagar gamla genom att fråga historiktabellen direkt.

Om du vill aktivera tidsbevaranderens rensning kör du följande Transact-SQL-uttryck efter tidsåterställning:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du använder temporala tabeller i dina program kan du läsa [Komma igång med temporala tabeller i Azure SQL Database](sql-database-temporal-tables.md).

Besök Kanal 9 för att höra en [riktig kund tidsmässig implementering framgångssaga](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [levande tidsmässig demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Mer information om temporala tabeller finns i [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn935015.aspx).
