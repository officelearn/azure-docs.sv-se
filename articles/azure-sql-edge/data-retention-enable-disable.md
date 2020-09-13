---
title: Aktivera och inaktivera data bevarande principer – Azure SQL Edge (för hands version)
description: Lär dig hur du aktiverar och inaktiverar data lagrings principer i Azure SQL Edge (för hands version)
keywords: SQL Edge, data kvarhållning
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 9787f2cfa87a16d9e7dd1753e4389977c6753b81
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550727"
---
# <a name="enable-and-disable-data-retention-policies"></a>Aktivera och inaktivera data lagrings principer

I det här avsnittet beskrivs hur du aktiverar och inaktiverar data lagrings principer för en databas och en tabell. 

## <a name="enable-data-retention-for-a-database"></a>Aktivera datakvarhållning för en databas

I följande exempel visas hur du aktiverar datakvarhållning med hjälp av [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options).

> [!NOTE]
> Aktivera funktionen för datakvarhållning i Azure SQL Edge (för hands version) genom att aktivera TF 12825 som ett start alternativ eller använda DBCC TRACEON-kommandot. Mer information om hur du aktiverar spårnings flaggor med hjälp av en MSSQL. conf-fil finns i [Konfigurera med hjälp av en MSSQL. conf-fil](configure.md#configure-by-using-an-mssqlconf-file). 

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Kontrol lera om data kvarhållning har Aktiver ATS för en databas

Följande kommando kan användas för att kontrol lera om data kvarhållning är aktiverat för en databas
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Aktivera datakvarhållning för en tabell

Data kvarhållning måste vara aktiverat för varje tabell som du vill att data ska rensas automatiskt. När datakvarhållning är aktiverat för databasen och tabellen, kommer en bakgrunds system aktivitet regelbundet att söka i tabellen för att identifiera och ta bort föråldrade (gamla) rader. Datakvarhållning kan aktive ras på en tabell antingen när tabellen skapas med hjälp av [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) eller med [Alter Table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

I följande exempel visas hur du aktiverar datakvarhållning för en tabell med hjälp av [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

Den `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` del av kommandot CREATE TABLE anger datakvarhållning i tabellen. Kommandot använder följande obligatoriska parametrar 

- DATA_DELETION-anger om data kvarhållning är på eller av.
- FILTER_COLUMN-namnet på kolumnen i tabellen som ska användas för att kontrol lera om raderna är föråldrade eller inte. Filter kolumnen kan bara vara en kolumn med följande data typer 
    - Datum
    - Datatyp
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD – ett heltals värde följt av en enhets beskrivning. De tillåtna enheterna är dag, vecka, månad och år.

I följande exempel visas hur du aktiverar datakvarhållning för tabellen med hjälp av [Alter Table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Kontrol lera om data kvarhållning har Aktiver ATS för en tabell

Följande kommando kan användas för att kontrol lera de tabeller för vilka datakvarhållning har Aktiver ATS

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Värdet data_retention_period =-1 och data_retention_period_unit som OÄNDLIGt anger att datakvarhållning inte har angetts för tabellen.

Följande fråga kan användas för att identifiera kolumnen som används som filter_column för datakvarhållning. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>Samrelaterade inställningar för databas-och tabell data lagring

Inställningen datakvarhållning i databasen och tabellen används tillsammans för att avgöra om autorensning för de inaktuella raderna ska köras i tabellerna eller inte. 

|Databas alternativ | Tabell alternativ | Beteende |
|----------------|--------------|----------|
| OFF | OFF | Principen för data bevarande är inaktive rad och både automatisk och manuell rensning av inaktuella poster är inaktive rad.|
| OFF | ON  | Principen för data bevarande är aktive rad för tabellen, men både Auto och manuell rensning av inaktuella poster är inaktiverat. |
| ON | OFF | Principen för data bevarande är aktive rad på databas nivå. Eftersom alternativet är inaktiverat på tabell nivå finns det dock ingen kvarhållning baserad rensning av inaktuella rader.|
| ON | ON | Principen för data bevarande är aktive rad för både databasen och tabellerna. Automatisk/manuell rensning av föråldrade poster har Aktiver ATS |

## <a name="disable-data-retention-on-a-table"></a>Inaktivera datakvarhållning av data i en tabell 

Datakvarhållning kan inaktive ras för en tabell med hjälp av [Alter Table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql). Följande kommando kan användas för att inaktivera datakvarhållning av data i en tabell.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Inaktivera datakvarhållning av data i en databas

Datakvarhållning kan inaktive ras för en tabell med hjälp av [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options). Följande kommando kan användas för att inaktivera datakvarhållning för en databas.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Nästa steg
- [Data lagring och automatisk data rensning](data-retention-overview.md)
- [Hantera historiska data med bevarande princip](data-retention-cleanup.md)
