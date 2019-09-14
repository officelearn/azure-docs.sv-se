---
title: Azure SQL Database viktig information | Microsoft Docs
description: Lär dig mer om de nya funktionerna och förbättringarna i Azure SQL Databases tjänsten och i Azure SQL Database-dokumentationen
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ef19d22b5c47c51f5ee3f74a4d7ab06725f7ed41
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968336"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL Database

Den här artikeln innehåller SQL Database funktioner som för närvarande finns i en offentlig för hands version. SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database). Uppdateringar och förbättringar av andra Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funktioner i offentlig för hands version

### <a name="single-databasetabsingle-database"></a>[Enkel databas](#tab/single-database)

| Funktion | Information |
| ---| --- |
| Accelererad databas återställning med enkla databaser och elastiska pooler | Mer information finns i [accelererad databas återställning](sql-database-accelerated-database-recovery.md).|
|Ungefärligt antal distinkta|Mer information finns i [Ungefärlig Count DISTINCT](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batch-läge på Rowstore (under kompatibilitetsnivå 150)|Mer information finns i [batch-läge på Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Dataidentifiering och -klassificering  |Mer information finns i [Azure SQL Database och SQL Data Warehouse data identifiering & klassificering](sql-database-data-discovery-and-classification.md).|
| Elastic Database-jobb | Mer information finns i [skapa, konfigurera och hantera elastiska jobb](elastic-jobs-overview.md). |
| Elastiska frågor | Mer information finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md). |
| Elastiska transaktioner | [Distribuerade transaktioner över moln databaser](sql-database-elastic-transactions-overview.md). |
|Feedback om minnes tilldelning (rad läge) (under kompatibilitetsnivå 150)|Mer information finns i [minnes beviljande feedback (rad läge)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Frågeredigeraren i Azure Portal |Mer information finns i [använda Azure Portal SQL-Frågeredigeraren för att ansluta och fråga efter data](sql-database-connect-query-portal.md).|
| R Services/Machine Learning med enkla databaser och elastiska pooler |Mer information finns i [Machine Learning Services i Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Återskapa förlorade databaser med hanterade instanser |Mer information finns i [Återskapa förlorade databaser i Azure SQL Managed instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replikering med hanterade instanser |Mer information finns i [Konfigurera replikering i en Azure SQL Database Hanterad instans databas](replication-with-sql-database-managed-instance.md).|
| Serverlös beräkningsnivå | Mer information finns i [SQL Database utan server (för hands version)](sql-database-serverless.md).|
|SQL-analys|Mer information finns i [Azure SQL-analys](../azure-monitor/insights/azure-sql.md).|
|Uppskjuten tabell variabel (under kompatibilitetsnivå 150)|Mer information finns i [tabell variabel uppskjuten kompilering](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Hot identifiering med hanterade instanser |Mer information finns i [Konfigurera hot identifiering i Azure SQL Database Hanterad instans](sql-database-managed-instance-threat-detection.md).|
| Transparent data kryptering (TDE) med Bring Your Own Key (BYOK) med hanterade instanser |Mer information finns [i Azure SQL Transparent datakryptering med Kundhanterade nycklar i Azure Key Vault: Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Hanterad instans](#tab/managed-instance)

| Funktion | Information |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Instans-pooler</a> | Ett bekvämt och kostnads effektivt sätt att migrera mindre SQL-instanser till molnet. |
| <a href="https://aka.ms/managed-instance-tde-byok">Ta med dina egna krypterings nycklar</a> | Migrera databasen från den lokala platsen som redan har aktiverat transparent datakryptering (TDE) med befintliga TDE-nycklar. |
| <a href="https://aka.ms/managed-instance-failover-groups">Geo-distribuerade failover-grupper</a> | Behåll en kopia av instansen i en annan region och se till att dina data blir tillgängliga även i det regionala katastrof scenariot. |
| <a href="https://aka.ms/managed-instance-aadlogins">Azure AD server-huvudobjekt på instans nivå (inloggningar)</a> | Skapa inloggningar på server nivå med hjälp av instruktionen <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning från extern provider</a> . |
| [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) | Replikera ändringarna från dina tabeller till andra databaser som har placerats på hanterade instanser, enskilda databaser eller SQL Server instanser eller uppdatera tabeller när vissa rader ändras i andra hanterade instanser eller SQL Server instans. |
| &nbsp; |

---

## <a name="updates"></a>Uppdateringar

En lista över SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database).

Uppdateringar och förbättringar av alla Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Bidra till innehåll

Information om hur du bidrar till Azure SQL Database-dokumentationen finns i [Guide för dokument bidrag](https://docs.microsoft.com/contribute/).
