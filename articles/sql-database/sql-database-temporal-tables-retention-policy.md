---
title: Hantera historiska data i Temporala tabeller med bevarandeprincip | Microsoft Docs
description: Lär dig hur du använder den temporala bevarandeprincip så att historiska data under din kontroll.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 09/25/2018
ms.date: 02/25/2019
ms.openlocfilehash: 62e88d912c55015f87cc00f21527010ad01ee00c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615677"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Hantera historiska data i Temporala tabeller med bevarandeprincip

Temporala tabeller kan öka databasstorleken, mer än vanliga tabeller, särskilt om du behåller historiska data under en längre tidsperiod. Därför är bevarandeprincipen för historiska data en viktig aspekt av planering och hantering av livscykeln för varje temporal tabell. Temporala tabeller i Azure SQL Database levereras med enkel att använda kvarhållning mekanism som hjälper dig att utföra den här uppgiften.

Temporala kvarhållning kan vara konfigurerad på nivån enskilda tabeller, vilket gör att användare att skapa flexibla föråldras principerna. Det är enkelt att tillämpa temporal kvarhållninsrensning: det krävs bara en parameter måste anges vid tabell skapas eller schemat ändras.

När du har definierat bevarandeprincip startar Azure SQL Database regelbundet kontrollera om det finns historiska rader som är kvalificerade för automatisk rensning. Identifiering av matchande rader och deras tas bort från historiktabellen sker transparent, i bakgrundsaktiviteten som är schemalagda och körs av systemet. Ålder villkor för historik tabellrader kontrolleras baserat på kolumnen som motsvarar slutet av SYSTEM_TIME-perioden. Om kvarhållningsperioden, exempelvis är inställt på sex månader, uppfylla tabellrader som är berättigade för rensning följande villkor:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

I föregående exempel, antas vi som **ValidTo** kolumnen som motsvarar slutet av SYSTEM_TIME-perioden.

## <a name="how-to-configure-retention-policy"></a>Så här konfigurerar du bevarandeprincip

Innan du konfigurerar bevarandeprincipen för en temporal tabell, kontrollera först om temporala historiska kvarhållning har aktiverats *på databasnivå*.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Databasen flaggan **is_temporal_history_retention_enabled** är inställt på på som standard, men användaren kan ändra den med instruktionen ALTER DATABASE. Den också automatiskt är inställt på OFF efter [tidpunkt för återställning](sql-database-recovery-using-backups.md) igen. Om du vill aktivera temporala lagringsrensningen för din databas, kör du följande uttryck:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Du kan konfigurera kvarhållning för temporala tabeller, även om **is_temporal_history_retention_enabled** har värdet OFF, men inte automatisk rensning för föråldrade rader utlöses i så fall.

Bevarandeprincipen är rätt när tabellen skapas genom att ange värdet för parametern in HISTORY_RETENTION_PERIOD:

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

Azure SQL Database kan du ange kvarhållningsperiod med olika tidsenheter: DAGAR, veckor, månader och år. Om in HISTORY_RETENTION_PERIOD utelämnas antas obegränsad kvarhållning. Du kan också använda oändlig nyckelordet uttryckligen.

I vissa situationer kan du konfigurera kvarhållning av säkerhetskopior när tabellen har skapats eller om du vill ändra tidigare konfigurerade värde. I det fallet använder du ALTER TABLE-instruktionen:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Ställa in SYSTEM_VERSIONING på OFF *bevaras inte* värdet för kvarhållning perioden. Ställa in SYSTEM_VERSIONING på ON utan in HISTORY_RETENTION_PERIOD anges uttryckligen resultat i obegränsad kvarhållningsperiod.

Om du vill granska aktuella tillståndet för policyn för datalagring i, Använd följande fråga som ansluter till temporal kvarhållninsrensning aktivering flaggan på databasnivå med kvarhållningsperioder för enskilda tabeller:

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


## <a name="how-sql-database-deletes-aged-rows"></a>Hur SQL-databas tar bort föråldrade rader

Rensningen är beroende av index layouten för historiktabellen. Det är viktigt att Observera att *endast tabellerna med distributionshistorik med ett grupperat index (B-träds- eller kolumnlagringsindex) kan ha begränsad kvarhållningsprincip konfigurerad*. En bakgrundsaktivitet skapas för att rensa föråldrade data för alla temporala tabeller med begränsad kvarhållningsperiod.
Rensa logik för att (B-trädet) klustrade radlagringsindexet tar bort föråldrade rad i mindre segment (upp till 10 K) genom att minimera trycket på logg- och i/o-undersystem. Även om rensning logic använder nödvändiga B-trädindex, ordningen på borttagningar för raderna som är äldre än kvarhållningsperioden inte kan garanteras ordentligt. Därför *inte vidtar någon beroendeövervakare beställts rensning i dina program*.

Uppgiften för grupperade columnstore tar bort hela [rad grupper](https://msdn.microsoft.com/library/gg492088.aspx) på samma gång (vanligtvis innehålla 1 miljon rader varje), vilket är ett mycket effektivt, särskilt när historiska data genereras i hög takt.

![Grupperade kvarhållning](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Utmärkt datakomprimering och effektiv kvarhållning Rensa gör klustrade columnstore-index det perfekta valet för scenarier när din arbetsbelastning genererar snabbt stora mängden historiska data. Mönstret är vanligt för intensiva [transaktionsbearbetning arbetsbelastningar som använder temporala tabeller](https://msdn.microsoft.com/library/mt631669.aspx) för ändringsspårning och granskning, trendanalys eller IoT datainmatning.

## <a name="index-considerations"></a>Index överväganden

Uppgiften för tabeller med rowstore grupperat index måste indexet att börja med den kolumnen motsvarande slutet av SYSTEM_TIME-perioden. Du kan inte konfigurera en begränsad kvarhållningsperiod om sådana index inte finns:

*Msg 13765, nivå 16, tillstånd 1 <br> </br> ställa in begränsad kvarhållningsperiod misslyckades för den temporala systemversionstabellen temporalstagetestdb.dbo.WebsiteUserInfo eftersom historiktabellen ' temporalstagetestdb.dbo.WebsiteUserInfoHistory' innehåller inte nödvändiga grupperade indexet. Överväg att skapa ett grupperat kolumnlagrings- eller B-trädindex från och med den kolumn som överensstämmer med slutet av SYSTEM_TIME period i historiktabellen.*

Det är viktigt att Observera att standard-historiktabellen som skapats av Azure SQL Database redan har det klustrade indexet som är godkända för policy för datalagring. Om du försöker ta bort index för en tabell med begränsad kvarhållningsperiod misslyckas med följande fel:

*Msg 13766, nivå 16, tillstånd 1 <br> </br> det går inte att släppa det grupperade indexet 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' eftersom den används för automatisk rensning av föråldrade data. Överväg att ställa in HISTORY_RETENTION_PERIOD på INFINITE för den motsvarande temporala systemversionstabellen om du vill ta bort det här indexet.*

Rensa på grupperade columnstore-indexet fungerar optimalt om historiska raderna infogas i stigande ordning (ordnade i slutet av periodkolumnen), som alltid är fallet när historiktabellen fylls uteslutande av mekanismen för SYSTEM_VERSIONIOING. Om rader i historiktabellen inte sorteras slutet av periodkolumnen (vilket kan vara fallet om du har migrerat befintliga historiska data), ska du återskapa kolumnlagringsindex ovanpå B-trädet radlagringsindexet korrekt beställda att uppnå optimal prestanda.

Undvik att återskapa kolumnlagringsindex för historiktabellen med begränsad kvarhållningsperiod eftersom det kan ändra ordning i radgrupper naturligt införts av systemversionshanteringen igen. Om du behöver återskapa kolumnlagringsindex i historiktabellen gör du genom att återskapa den ovanpå kompatibla B-trädindex, bevarar ordning i radgrupper krävs för vanliga slutfasen i rensningen. Samma metod som vidtas om du skapar den temporala tabellen med befintliga historiktabellen som har det klustrade kolumnindex utan garanterad dataordningen:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

När begränsad kvarhållningsperiod har konfigurerats för historiktabellen med klustrade columnstore-index kan skapa du inte ytterligare icke-grupperade B-trädindex i tabellen:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Ett försök att köra ovan instruktionen misslyckas med följande fel:

*Msg 13772, nivå 16, tillstånd 1 <br> </br> kan inte skapa icke-grupperat index på den temporala historiktabellen 'WebsiteUserInfoHistory' eftersom den har en begränsad kvarhållningsperiod och grupperade columnstore-indexet som definierats.*

## <a name="querying-tables-with-retention-policy"></a>Skicka frågor till tabeller med bevarandeprincip

Alla frågor på den temporala tabellen filtrerar automatiskt bort historiska rader som matchar begränsad kvarhållningsprincip för att undvika oberäkneliga och inkonsekventa resultat, eftersom föråldrade rader kan tas bort av rensningsuppgiften i *när som helst i tid och på valfri ordning*.

Följande bild visar frågeplanen för en enkel fråga:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Frågeplanen innehåller ytterligare filter som tillämpas på slutet av periodkolumnen (ValidTo) i operatorn klustrade Index skanna för historiktabellen (markerat). Det här exemplet förutsätts att kvarhållningsperiod för en månad har ställts in för WebsiteUserInfo tabell.

![Frågefilter för kvarhållning](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Om du frågar historiktabellen direkt kan du se rader som är äldre än angivna period, men utan någon garanti för upprepningsbara frågeresultat. Följande bild visar körning av frågeplan för frågan i historiktabellen utan ytterligare filter:

![Fråga historik utan kvarhållning filter](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Förlita dig inte din affärslogik vid läsning av historiktabellen utöver kvarhållningsperioden som du kan få inkonsekventa eller oväntade resultat. Vi rekommenderar att du använder temporala frågor med FOR SYSTEM_TIME-sats för att analysera data i temporala tabeller.

## <a name="point-in-time-restore-considerations"></a>Peka i tiden återställning överväganden

När du skapar en ny databas genom att [återställning befintlig databas till en viss punkt i tiden](sql-database-recovery-using-backups.md), den har temporal kvarhållninsrensning inaktiverad på databasnivå. (**is_temporal_history_retention_enabled** flaggan inställt på OFF). Den här funktionen kan du undersöka alla historiska rader vid en återställning, utan att behöva bekymra dig att föråldrade rader har tagits bort innan du kommer att skicka frågor mot dem. Du kan använda den för att *granska historiska data utöver konfigurerade kvarhållningsperiod*.

Anta att en temporal tabell har en månad kvarhållningen angiven tidsperiod. Om din databas har skapats på Premium-tjänstnivån, skulle du kunna skapa databaskopia med databastillståndet upp till 35 dagar tillbaka i tiden. Som ett effektivt sätt skulle gör det möjligt att analysera historiska rader som är upp till 65 dagar genom att fråga historiktabellen direkt.

Om du vill aktivera temporala lagringsrensningen kör du följande Transact-SQL-instruktionen efter punkt återställning till tidpunkt:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du använder Temporala tabeller i dina program, Kolla in [komma igång med Temporala tabeller i Azure SQL Database](sql-database-temporal-tables.md).

Gå till Channel 9 att höra en [framgångshistoria för kundens verkliga temporala implementering](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [live temporala demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Detaljerad information om Temporala tabeller, granska [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn935015.aspx).
