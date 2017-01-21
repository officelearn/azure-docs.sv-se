---
title: "Översikt över funktioner i Azure SQL Database | Microsoft Docs"
description: "Den här sidan innehåller en översikt över Azure SQL Database logiska servrar och databaser och innehåller en matris över funktionsstöds med länkar för varje listad funktion."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: c153f09741b9b063d67459bbb127f9c4e7942a5b


---
# <a name="azure-sql-database-features"></a>Azure SQL Database-funktioner
Det här ämnet innehåller en översikt över Azure SQL Database logiska servrar och databaser och innehåller en matris över funktionsstöds med länkar för varje listad funktion. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Vad är en logisk Azure SQL Database-server?
En logisk Azure SQL Database-server fungerar som en central administrativ plats för flera databaser. I SQL Database är en server en logisk konstruktion som skiljer sig från en SQL Server-instans, som du kanske är bekant med i den lokala miljön. Mer specifikt ger SQL Database-tjänsten inga garantier avseende platsen för databaserna i förhållande till deras logiska servrar och exponerar inga funktioner eller åtkomst på instansnivå. Mer information om logiska Azure SQL-servrar finns i avsnittet om [logiska servrar](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>Vad är en Azure SQL Database?
Varje databas i Azure SQL Database är associerad med en logisk server. Databasen kan vara:

- En enkel databas med dess [uppsättning resurser](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU:er)
- En del av en [pool med databaser](sql-database-elastic-pool.md) som [delar en uppsättning resurser](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU:er)
- En del av en [utskalad uppsättning delade databaser](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), som kan vara enskilda databaser eller databaser i en pool
- En del av en uppsättning databaser som ingår i ett [SaaS-designmönster för flera klienter](sql-database-design-patterns-multi-tenancy-saas-applications.md), vars databaser kan vara enskilda databaser eller databaser i en pool (eller båda) 

Mer information om Azure SQL-databaser finns i [SQL-databaser](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Vilka funktioner stöds?

Följande tabeller visar de viktigaste funktionerna i Azure SQL Database och SQL Server, anger dess support och innehåller en länk till mer information om funktionen på varje plattform. Du hittar Transact-SQL-funktioner om du följer länken i tabellen för funktionens kategori. Mer information om varför vissa funktioner inte stöds finns i avsnittet [Skillnader mellan Azure SQL Database och Transact-SQL](sql-database-transact-sql-information.md).

Vi fortsätter att lägga till funktioner till V12. Så vi rekommenderar att du besöker vår webbsida för tjänstuppdateringar för Azure och dess filter:

* Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrerade till allmän tillgänglighet [meddelanden (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

> [!TIP]
> Om du vill testa en befintlig databas för kompatibilitet med Azure SQL Database finns information i [Validera Azure SQL Database-kompatibilitet](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
>

| **Funktion** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| Aktiv geo-replikering | Finns inte stöd för – [AlwaysOn-Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) | [Stöds](sql-database-geo-replication-overview.md)
| Alltid krypterad | [Stöds](https://msdn.microsoft.com/library/mt163865.aspx) | [Stöds](sql-database-always-encrypted.md) |
| AlwaysOn-tillgänglighetsgrupper | [Stöds](https://msdn.microsoft.com/library/hh510230.aspx) | Finns inte stöd för – [aktiv geo-replikering](sql-database-geo-replication-overview.md) |
| Ansluta en databas | [Stöds](https://msdn.microsoft.com/library/ms190209.aspx) | Stöds inte |
| Programroller | [Stöds](https://msdn.microsoft.com/library/ms190998.aspx) | [Stöds](https://msdn.microsoft.com/library/ms190998.aspx) |
| Autoskala | Stöds inte | [Stöds](sql-database-scale-up.md) |
| Azure Active Directory | Stöds inte | [Stöds](sql-database-aad-authentication.md) |
| Azure Data Factory | Finns inte stöd för – [SQL Server Integration Services (SSIS)](https://msdn.microsoft.com/library/ms141026.aspx) | [Stöds](https://azure.microsoft.com/services/data-factory/) |
| Granskning | [Stöds](https://msdn.microsoft.com/library/cc280386.aspx) | [Stöds](sql-database-auditing-get-started.md) |
| BACPAC-fil (exportera) | [Stöds](https://msdn.microsoft.com/library/hh213241.aspx) | [Stöds](sql-database-export.md) |
| BACPAC-fil (importera) | [Stöds](https://msdn.microsoft.com/library/hh710052.aspx) | [Stöds](sql-database-import.md) |
| Uttryck för SÄKERHETSKOPIERING och ÅTERSTÄLLNING  | [Stöds](https://msdn.microsoft.com/library/ff848768.aspx) | Stöds inte |
| Inbyggda funktioner | [Stöds](https://msdn.microsoft.com/library/ms174318.aspx) | [De flesta](https://msdn.microsoft.com/library/ms174318.aspx) |
| Registrering av ändringsdata | [Stöds](https://msdn.microsoft.com/library/cc645937.aspx) | Stöds inte |
| Spårning av ändringar | [Stöds](https://msdn.microsoft.com/library/bb933875.aspx) | [Stöds](https://msdn.microsoft.com/library/bb933875.aspx) |
| Sortering av uttryck | [Stöds](https://msdn.microsoft.com/library/ff848763.aspx) | [Stöds](https://msdn.microsoft.com/library/ff848763.aspx) |
| Columnstore-index | [Stöds](https://msdn.microsoft.com/library/gg492088.aspx) | [Endast Premium edition](https://msdn.microsoft.com/library/gg492088.aspx) |
| CLR (Common language runtime) | [Stöds](https://msdn.microsoft.com/library/ms131102.aspx) | Stöds inte |
| Inneslutna databaser | [Stöds](https://msdn.microsoft.com/library/ff929071.aspx) | Inbyggd |
| Inneslutna användare | [Stöds](https://msdn.microsoft.com/library/ff929188.aspx) | [Stöds](sql-database-manage-logins.md#non-administrator-users) |
| Kontroll av nyckelord för flödesspråk | [Stöds](https://msdn.microsoft.com/library/ms174290.aspx) | [Stöds](https://msdn.microsoft.com/library/ms174290.aspx) |
| Frågor över flera databaser | [Stöds](https://msdn.microsoft.com/library/dn584627.aspx) | [Elastiska frågor](sql-database-elastic-query-overview.md) |
| Markörer | [Stöds](https://msdn.microsoft.com/library/ms181441.aspx) | [Stöds](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Datakomprimering | [Stöds](https://msdn.microsoft.com/library/cc280449.aspx) | [Stöds](https://msdn.microsoft.com/library/cc280449.aspx) |
| Säkerhetskopior av databasen | [Visas för användare](https://msdn.microsoft.com/library/ms187048.aspx) | [Inbyggd](sql-database-automated-backups.md) |
| Database-mail | [Stöds](https://msdn.microsoft.com/library/ms189635.aspx) | Stöds inte |
| Databasspegling | [Stöds](https://msdn.microsoft.com/library/ms189852.aspx) | Stöds inte |
| Konfigurationsalternativ för databasen | [Stöds](https://msdn.microsoft.com/library/mt629158.aspx) | [Stöds](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Stöds](https://msdn.microsoft.com/library/ff877925.aspx) | Stöds inte |
| Ögonblicksbilder av databas | [Stöds](https://msdn.microsoft.com/library/ms175158.aspx) | Stöds inte |
| Datatyper | [Stöds](https://msdn.microsoft.com/library/ms187752.aspx) | [Stöds](https://msdn.microsoft.com/library/ms187752.aspx) |  
| DBCC-uttryck | [Alla](https://msdn.microsoft.com/library/ms188796.aspx) | [Vissa](https://msdn.microsoft.com/library/ms188796.aspx) |
| DDL-uttryck | [Stöds](https://msdn.microsoft.com/library/ff848799.aspx) | [De flesta](https://msdn.microsoft.com/library/ff848799.aspx)
| DDL-utlösare | [Stöds](https://msdn.microsoft.com/library/ms175941.aspx) | [Endast databas](https://msdn.microsoft.com/library/ms175941.aspx) |
| Distribuerade transaktioner | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Begränsad endast intra-SQL Database-scenarier |
| DML-uttryck | [Stöds](https://msdn.microsoft.com/library/ff848766.aspx) | [De flesta](https://msdn.microsoft.com/library/ff848766.aspx) |
| DML-utlösare | [Stöds](https://msdn.microsoft.com/library/ms178110.aspx) | [Stöds](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [Alla](https://msdn.microsoft.com/library/ms188754.aspx) | [Vissa](https://msdn.microsoft.com/library/ms188754.aspx) |
| elastiska pooler | Stöds inte | [Stöds](sql-database-elastic-pool.md) |
| Elastiska jobb | Finns inte stöd för – [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Stöds](sql-database-elastic-jobs-getting-started.md) | 
| Elastiska frågor | Finns inte stöd för – [Frågor över flera databaser](https://msdn.microsoft.com/library/dn584627.aspx) | [Stöds](sql-database-elastic-query-overview.md) |
| Händelseaviseringar | [Stöds](https://msdn.microsoft.com/library/ms186376.aspx) | [Stöds](sql-database-insights-alerts-portal.md) |
| Uttryck | [Stöds](https://msdn.microsoft.com/library/ms190286.aspx) | [Stöds](https://msdn.microsoft.com/library/ms190286.aspx) |
| Utökade händelser | [Stöds](https://msdn.microsoft.com/library/bb630282.aspx) | [Vissa](sql-database-xevent-db-diff-from-svr.md) |
| Utökade lagrade procedurer | [Stöds](https://msdn.microsoft.com/library/ms164627.aspx) | Stöds inte |
| Filgrupper | [Stöds](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Endast primär](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| FileStream | [Stöds](https://msdn.microsoft.com/library/gg471497.aspx) | Stöds inte |
| Fulltextsökning | [Stöds](https://msdn.microsoft.com/library/ms142571.aspx) | [Orddelare från tredje part som inte stöds](https://msdn.microsoft.com/library/ms142571.aspx) |
| Funktioner | [Stöds](https://msdn.microsoft.com/library/ms174318.aspx) | [De flesta](https://msdn.microsoft.com/library/ms174318.aspx) |
| Minnesintern optimering | [Stöds](https://msdn.microsoft.com/library/dn133186.aspx) | [Endast Premium edition](https://msdn.microsoft.com/library/dn133186.aspx) |
| Jobb | [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Stöds](sql-database-elastic-jobs-getting-started.md) |
| Stöd för JSON-data | [Stöds](https://msdn.microsoft.com/library/dn921897.aspx) | [Stöds](sql-database-json-features.md) |
| Språkelement | [Stöds](https://msdn.microsoft.com/library/ff848807.aspx) | [De flesta](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Länkade servrar | [Stöds](https://msdn.microsoft.com/library/ms188279.aspx) | Finns inte stöd för – [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) |
| Loggöverföring | [Stöds](https://msdn.microsoft.com/library/ms187103.aspx) | Finns inte stöd för – [aktiv geo-replikering](sql-database-geo-replication-overview.md) |
| Kommandon för hantering | [Stöds](https://msdn.microsoft.com/library/ms190286.aspx)| [Stöds inte](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Stöds](https://msdn.microsoft.com/library/ff487003.aspx) | Stöds inte |
| Minimal loggning i massimport | [Stöds](https://msdn.microsoft.com/library/ms190422.aspx) | Stöds inte |
| Ändra systemdata | [Stöds](https://msdn.microsoft.com/library/ms178028.aspx) | Stöds inte |
| Indexåtgärder online | [Stöds](https://msdn.microsoft.com/library/ms177442.aspx) | [Transaktionstorlek begränsas av tjänstnivå](https://msdn.microsoft.com/library/ms177442.aspx) |
| Operatörer | [Stöds](https://msdn.microsoft.com/library/ms174986.aspx) | [De flesta](https://msdn.microsoft.com/library/ms174986.aspx) |
| Återställning till tidpunkt av databas | [Stöds](https://msdn.microsoft.com/library/ms179451.aspx) | [Stöds](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Stöds](https://msdn.microsoft.com/library/mt143171.aspx) | [Stöds inte]
| Principbaserad hantering | [Stöds](https://msdn.microsoft.com/library/bb510667.aspx) | Stöds inte |
| Predikat | [Stöds](https://msdn.microsoft.com/library/ms189523.aspx) | [De flesta](https://msdn.microsoft.com/library/ms189523.aspx)
| Resursstyrning | [Stöds](https://msdn.microsoft.com/library/bb933866.aspx) | [Inbyggd](sql-database-service-tiers.md) |
| Återställ databasen från en säkerhetskopia | [Stöds](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Från endast inbyggda säkerhetskopior](sql-database-recovery-using-backups.md) |
| Säkerhet på radnivå | [Stöds](https://msdn.microsoft.com/library/dn765131.aspx) | [Stöds](https://msdn.microsoft.com/library/dn765131.aspx) |
| Säkerhetsuttryck | [Stöds](https://msdn.microsoft.com/library/ff848791.aspx) | [Vissa](https://msdn.microsoft.com/library/ff848791.aspx) |
| Semantisk sökning | [Stöds](https://msdn.microsoft.com/library/gg492075.aspx) | Stöds inte |
| Sekvensnummer | [Stöds](https://msdn.microsoft.com/library/ff878058.aspx) | [Stöds](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [Stöds](https://msdn.microsoft.com/library/bb522893.aspx) | Stöds inte |
| Konfigurationsalternativ för server | [Stöds](https://msdn.microsoft.com/library/ms189631.aspx) | Finns inte stöd för – [Konfigurationsalternativ för databasen](https://msdn.microsoft.com/library/mt629158.aspx) |
| Ange uttryck | [Stöds](https://msdn.microsoft.com/library/ms190356.aspx) | [De flesta](https://msdn.microsoft.com/library/ms190356.aspx) 
| Spatial | [Stöds](https://msdn.microsoft.com/library/bb933790.aspx) | [Stöds](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server Agent | [Stöds](https://msdn.microsoft.com/library/ms189237.aspx) | Finns inte stöd för – [elastiska jobb](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Stöds](https://msdn.microsoft.com/library/bb522607.aspx) | Finns inte stöd för – [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Stöds](https://msdn.microsoft.com/library/ms141026.aspx) | Finns inte stöd för – [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Stöds](https://msdn.microsoft.com/library/hh245198.aspx) | [Stöds](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server-profiler | [Stöds](https://msdn.microsoft.com/library/ms181091.aspx) | Finns inte stöd för – [utökade händelser](https://msdn.microsoft.com/library/ms181091.aspx) |
| SQL Server-replikering | [Stöds](https://msdn.microsoft.com/library/ms151198.aspx) | [Prenumerant för tansaktions-och ögonblicksbildsreplikering endast](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SQL Server Reporting Services (SSRS) | [Stöds](https://msdn.microsoft.com/library/ms159106.aspx) | Stöds inte |
| Lagrade procedurer | [Stöds](https://msdn.microsoft.com/library/ms190782.aspx) | [Stöds](https://msdn.microsoft.com/library/ms190782.aspx) |
| Systemlagrade funktioner | [Stöds](https://msdn.microsoft.com/library/ff848780.aspx) | [Vissa](https://msdn.microsoft.com/library/ff848780.aspx) |
| Systemlagrade procedurer | [Stöds](https://msdn.microsoft.com/library/ms187961.aspx) | [Vissa](https://msdn.microsoft.com/library/ms187961.aspx) |
| Systemtabeller | [Stöds](https://msdn.microsoft.com/library/ms179932.aspx) | [Vissa](https://msdn.microsoft.com/library/ms179932.aspx) |
| Systemvyer | [Stöds](https://msdn.microsoft.com/library/ms177862.aspx) | [Vissa](https://msdn.microsoft.com/library/ms177862.aspx)
| Tabellpartitionering | [Stöds](https://msdn.microsoft.com/library/ms190787.aspx) | [Endast primär filgrupp](https://msdn.microsoft.com/library/ms190787.aspx) |
| Temporära tabeller | [Lokala och globala](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Endast lokala](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Temporala tabeller | [Stöds](https://msdn.microsoft.com/library/dn935015.aspx) | [Stöds](sql-database-temporal-tables.md) |
| Transaktionsuttryck | [Stöds](https://msdn.microsoft.com/library/ms174377.aspx) | [Stöds](https://msdn.microsoft.com/library/ms174377.aspx) |
| Variabler | [Stöds](https://msdn.microsoft.com/library/ff848809.aspx) | | [Stöds](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Transparent datakryptering (TDE)  | [Stöds](https://msdn.microsoft.com/library/bb934049.aspx) | [Stöds](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server-redundansklustring | [Stöds](https://msdn.microsoft.com/library/hh270278.aspx) | Finns inte stöd för – [aktiv geo-replikering](sql-database-geo-replication-overview.md) |
| XML-index | [Stöds](http://msdn.microsoft.com/library/bb934097.aspx) | [Stöds](http://msdn.microsoft.com/library/bb934097.aspx) |
| XML-uttryck | [Stöds](https://msdn.microsoft.com/library/ff848798.aspx) | [Stöds](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- En översikt över logiska Azure SQL-servrar finns i [SQL Database logical server overview](sql-database-server-overview.md) (Översikt över logiska SQL Database-servrar)
- En översikt över Azure SQL-databaser finns i [Översikt över SQL Database](sql-database-overview.md)
- Information om stöd för och skillnader i Transact-SQL finns i [Skillnader mellan Azure SQL Database och Transact-SQL](sql-database-transact-sql-information.md).
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**. En översikt över tjänstnivåer finns i avsnittet om [tjänstnivåer för SQL Database](sql-database-service-tiers.md).
- En översikt över säkerhet finns i [Azure SQL Database-säkerhetsöversikt](sql-database-security-overview.md).
- Mer information om tillgängliga drivrutiner och stöd för SQL Database finns i [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) (Anslutningsbibliotek för SQL Database och SQL Server).



<!--HONumber=Dec16_HO4-->


