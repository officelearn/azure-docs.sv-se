---
title: Azure SQL Data Warehouse versions information juni 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67626158"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Juni 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i juni 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Användardefinierade återställnings punkter
SQL Data Warehouse automatiskt tar ögonblicks bilder av ditt informations lager var 8: e timme som garanterar ett 8-timmars återställnings punkt mål (återställnings punkt mål). Även om det här automatiserade ögonblicks bilderna underlättar hanteringen av ditt informations lager, behöver du ta ögonblicks bilder vid kritiska tider baserat på ditt företags behov. Anta till exempel att du tar en ögonblicks bild direkt före en betydande data belastning eller distributionen av nya skript till data lagret för att aktivera en återställnings punkt direkt före åtgärden. 

SQL Data Warehouse stöder nu [användardefinierade återställnings punkter](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) via cmdleten [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) .

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Säkerhet på kolumn nivå
Det är viktigt att hantera data åtkomst och säkerhet i ditt informations lager för att skapa förtroende med dina kunder och partner. SQL Data Warehouse [har nu stöd för säkerhet på kolumn nivå (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) som gör att du kan justera behörigheter för att Visa känsliga data genom att begränsa användar åtkomsten till vissa kolumner i tabellerna utan att behöva designa om ditt informations lager.

Med CLS kan du styra åtkomsten till tabell kolumner baserat på användarens körnings kontext eller grupp medlemskap med hjälp av standard- [Grant](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) -SQL-instruktionen. Logiken för åtkomst begränsning finns i själva databas nivån i stället för bort från data i ett annat program, vilket fören klar den övergripande säkerhets implementeringen.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
Funktionen [OBJECT_SCHEMA_NAME ()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) returnerar databasens schema namn för objekt som omfattas av schemat. Den här funktionen har blivit vanliga i ETL-verktyg vid verifiering av objekt schema. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Exempel resultat**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Stöd för sys. time_zone_info-vyn
Vyn [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) returnerar information om de tids zoner som stöds inom Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Exempel resultat**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Automatiska statistik åtgärder visas i sys. DM _pdw_exec_requests (beteende ändring)

Med introduktionen av [Automatisk skapande av statistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic)kommer Azure SQL Data Warehouse att generera statistik för att optimera frågekörningen. Versionen från juni 2018 ger dig möjlighet att övervaka när statistik genereras automatiskt genom att lägga till en post i vyn [sys. DM _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) när en åtgärd för att [skapa statistik](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) utförs.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Exempel resultat**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapar en SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp eftersom du kan stöta på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
