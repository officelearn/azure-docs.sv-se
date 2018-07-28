---
title: Azure SQL Data Warehouse viktig juni 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 1db503ea40a9f07720aa9c130cd3bcc22f87f2af
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324827"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Juni 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i juni 2018. 

## <a name="user-defined-restore-points"></a>Användardefinierad återställningspunkter
SQL Data Warehouse tar automatiskt ögonblicksbilder av ditt informationslager var åttonde timme, vilket ger en snabbutvärdering på åtta timmar mål för återställningspunkt (RPO). Detta automatiserad ögonblicksbilder enkel hanteringsbelastning för att köra ditt informationslager, finns men det ett behov av att ta bilder av kritiska perioder baserat på företagets behov. Till exempel ta en ögonblicksbild precis före en stor datainläsning eller distribution av nya skript till datalagret för att aktivera en återställningspunkt precis före åtgärden. 

SQL Data Warehouse har nu stöd [användardefinierade återställningspunkter](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) via den [New AzureRmSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoin) cmdlet.

```PowerShell
New-AzureRmSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Kolumnen på radnivå
Hantera dataåtkomst och säkerhet i ditt data warehouse är avgörande för att skapa förtroende med dina kunder och partner. SQL Data Warehouse [stöder nu på kolumnnivå säkerhet (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) som gör det möjligt att justera behörigheterna för att visa känsliga data genom att begränsa användaråtkomst till specifika kolumner i dina tabeller utan att behöva ändra designen på ditt informationslager.

CLS gör att du kan styra åtkomsten till tabellkolumner baserat på användarens körningskontexten skapades eller deras gruppmedlemskap genom att använda [BEVILJA](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL-instruktionen. Logiken för begränsningen finns på databasnivån själva i stället för från data i ett annat program, förenkla implementeringen övergripande säkerhet.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
Den [OBJECT_SCHEMA_NAME()]() funktionen returnerar databasnamnet för schemat för schema-omfattande objekt. Den här funktionen har blivit vanliga i ETL-verktyg när objektet schemavalidering. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Exempel på resultat**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Stöd för vyn sys.time_zone_info
Den [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) visa returnerar information om tidszonerna som stöds i Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Exempel på resultat**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Automatisk statistik åtgärder visas i sys.dm_pdw_exec_requests (Funktionsändring)

Med introduktionen av [automatisk Create Statistics](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics), Azure SQL Data Warehouse genererar statistik för att optimera Frågekörningen. Versionen för juni 2018 lägger till möjligheten att övervaka när stats genereras automatiskt genom att lägga till en post i den [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) granska när någon [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) utförs.

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
**Exempel på resultat**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

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
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md