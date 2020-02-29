---
title: Instruktioner för T-SQL
description: Länkar till dokumentationen för T-SQL-uttryck som stöds i SQL Analytics.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d37e1b727829505940b77c7a82293f90050a87c0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198362"
---
# <a name="t-sql-statements-supported-in-sql-analytics"></a>T-SQL-uttryck som stöds i SQL Analytics
Länkar till dokumentationen för T-SQL-uttryck som stöds i SQL Analytics.

## <a name="data-definition-language-ddl-statements"></a>DDL-uttryck (Data Definition Language)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ÄNDRA INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [Ändra materialiserad vy](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (förhands granskning) 
* [ÄNDRA PROCEDUR](https://msdn.microsoft.com/library/ms189762.aspx)
* [ÄNDRA SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [SKAPA COLUMNSTORE-INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [SKAPA DATABAS](https://msdn.microsoft.com/library/mt204021.aspx)
* [SKAPA DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
* [SKAPA EXTERN DATA KÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx)
* [SKAPA FUNKTION](https://msdn.microsoft.com/library/mt203952.aspx)
* [SKAPA INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Skapa materialiserad vy som Välj](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (förhands granskning) 
* [SKAPA PROCEDUR](https://msdn.microsoft.com/library/ms187926.aspx)
* [SKAPA SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [SKAPA VY](https://msdn.microsoft.com/library/ms187956.aspx)
* [SKAPA KLASSIFICERING AV ARBETS BELASTNING](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [SLÄPP EXTERN DATA KÄLLA](https://msdn.microsoft.com/library/mt146367.aspx)
* [SLÄPP EXTERNT FIL FORMAT](https://msdn.microsoft.com/library/mt146379.aspx)
* [SLÄPP EXTERN TABELL](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [TA BORT PROCEDUR](https://msdn.microsoft.com/library/ms174969.aspx)
* [SLÄPP STATISTIK](https://msdn.microsoft.com/library/ms175075.aspx)
* [TA BORT TABELL](https://msdn.microsoft.com/library/ms173790.aspx)
* [TA BORT SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [SLÄPP VY](https://msdn.microsoft.com/library/ms173492.aspx)
* [TA BORT KLASSIFICERING AV ARBETS BELASTNING](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [TA](https://msdn.microsoft.com/library/mt631611.aspx)
* [ANGE RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>DML-uttryck (Data Manipulation Language)
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPPDATERA](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Databas konsol kommandon
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (för hands version)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (för hands version)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Frågeuttryck
* [SELECT](https://msdn.microsoft.com/library/ms189499.aspx)
* [MED common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [UTOM och Intersect](https://msdn.microsoft.com/library/ms188055.aspx)
* [Vad](https://msdn.microsoft.com/library/mt631615.aspx)
* [SOM](https://msdn.microsoft.com/library/ms177634.aspx)
* [Använda PIVOT och unpivot](https://msdn.microsoft.com/library/ms177410.aspx)
* [GRUPPERA EFTER](https://msdn.microsoft.com/library/ms177673.aspx)
* [Jag](https://msdn.microsoft.com/library/ms180199.aspx)
* [SORTERA EFTER](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [VILKEN](https://msdn.microsoft.com/library/ms188047.aspx)
* [ÖVERSTA](https://msdn.microsoft.com/library/ms189463.aspx)
* [Utjämning](https://msdn.microsoft.com/library/mt631614.aspx)
* [Sök villkor](https://msdn.microsoft.com/library/ms173545.aspx)
* [Under frågor](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Säkerhetsuttryck
* Behörigheter: [bevilja](https://msdn.microsoft.com/library/ms187965.aspx), [neka](https://msdn.microsoft.com/library/ms188338.aspx), [återkalla](https://msdn.microsoft.com/library/ms187728.aspx)
* [ÄNDRA AUKTORISERING](https://msdn.microsoft.com/library/ms187359.aspx)
* [ÄNDRA CERTIFIKAT](https://msdn.microsoft.com/library/ms189511.aspx)
* [ÄNDRA DATABAS KRYPTERINGS NYCKEL](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ÄNDRA HUVUD NYCKEL](https://msdn.microsoft.com/library/ms186937.aspx)
* [ÄNDRA ROLL](https://msdn.microsoft.com/library/ms189775.aspx)
* [ÄNDRA ANVÄNDARE](https://msdn.microsoft.com/library/ms176060.aspx)
* [SÄKERHETSKOPIERA CERTIFIKAT](https://msdn.microsoft.com/library/ms178578.aspx)
* [STÄNG HUVUD NYCKEL](https://msdn.microsoft.com/library/ms188387.aspx)
* [SKAPA CERTIFIKAT](https://msdn.microsoft.com/library/ms187798.aspx)
* [SKAPA DATABAS KRYPTERINGS NYCKEL](https://msdn.microsoft.com/library/bb677241.aspx)
* [SKAPA INLOGGNING](https://msdn.microsoft.com/library/ms189751.aspx)
* [SKAPA HUVUD NYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
* [SKAPA ROLL](https://msdn.microsoft.com/library/ms187936.aspx)
* [SKAPA ANVÄNDARE](https://msdn.microsoft.com/library/ms173463.aspx)
* [SLÄPP CERTIFIKAT](https://msdn.microsoft.com/library/ms179906.aspx)
* [TA BORT DATABAS KRYPTERINGS NYCKEL](https://msdn.microsoft.com/library/bb630256.aspx)
* [SLÄPP INLOGGNING](https://msdn.microsoft.com/library/ms188012.aspx)
* [SLÄPP HUVUD NYCKEL](https://msdn.microsoft.com/library/ms180071.aspx)
* [TA BORT ROLL](https://msdn.microsoft.com/library/ms174988.aspx)
* [SLÄPP ANVÄNDARE](https://msdn.microsoft.com/library/ms189438.aspx)
* [ÖPPNA HUVUD NYCKEL](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [språk element för T-SQL i SQL Analytics](sql-data-warehouse-reference-tsql-language-elements.md)och [systemvyer i SQL Analytics](sql-data-warehouse-reference-tsql-system-views.md).
