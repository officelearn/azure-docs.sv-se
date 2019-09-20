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
ms.openlocfilehash: 90482fc859c4dcdd04a70e91da2fd6adb079a795
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162279"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL Database

Den här artikeln innehåller SQL Database funktioner som för närvarande finns i en offentlig för hands version. SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database). Uppdateringar och förbättringar av andra Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funktioner i offentlig för hands version

### <a name="single-databasetabsingle-database"></a>[Enkel databas](#tab/single-database)

| Funktion | Information |
| ---| --- |
| [Privat Azure-länk](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Private Link förenklar nätverksarkitekturen och skyddar anslutningen mellan slutpunkter i Azure genom att behålla data på Azure-nätverket, vilket eliminerar exponeringen på Internet. Med Private Link kan du även skapa och återge dina egna tjänster på Azure. |
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
| Serverlös beräkningsnivå | Mer information finns i [SQL Database utan server (för hands version)](sql-database-serverless.md).|
|SQL-analys|Mer information finns i [Azure SQL-analys](../azure-monitor/insights/azure-sql.md).|
|Uppskjuten tabell variabel (under kompatibilitetsnivå 150)|Mer information finns i [tabell variabel uppskjuten kompilering](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Hanterad instans](#tab/managed-instance)

| Funktion | Information |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Instans-pooler</a> | Ett bekvämt och kostnads effektivt sätt att migrera mindre SQL-instanser till molnet. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparent data kryptering (TDE) med Bring Your Own Key (BYOK)</a> |Mer information finns [i Azure SQL Transparent datakryptering med Kundhanterade nycklar i Azure Key Vault: Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Azure AD server-huvudobjekt på instans nivå (inloggningar)</a> | Skapa inloggningar på server nivå med hjälp av instruktionen <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning från extern provider</a> . |
| [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) | Replikera ändringarna från dina tabeller till andra databaser som har placerats på hanterade instanser, enskilda databaser eller SQL Server instanser eller uppdatera tabeller när vissa rader ändras i andra hanterade instanser eller SQL Server instans. Mer information finns i [Konfigurera replikering i en Azure SQL Database Hanterad instans databas](replication-with-sql-database-managed-instance.md). |
| Identifiering av hot |Mer information finns i [Konfigurera hot identifiering i Azure SQL Database Hanterad instans](sql-database-managed-instance-threat-detection.md).|
| Återskapa förlorade databaser med hanterade instanser |Mer information finns i [Återskapa förlorade databaser i Azure SQL Managed instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Nya funktioner

### <a name="managed-instance-h2-2019-updates"></a>Hanterad instans H2 2019 uppdateringar

- Med [grupper för automatisk redundans](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kan du replikera alla databaser från den primära instansen till en sekundär instans i en annan region.
- Konfigurera din hanterade instans beteende med [globala spårnings flaggor](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Hanterad instans H1 2019-uppdateringar

Följande funktioner är aktiverade i distributions modellen för hanterade instanser i H1 2019:
  - Support för prenumerationer med <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Azures månatliga kredit för Visual Studio-prenumeranter</a> och ökade [regionala gränser](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Stöd för <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 och SharePoint 2019 </a> och <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Skapa instanser med <a href="https://aka.ms/managed-instance-collation">sortering på server nivå</a> och <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">tidszon</a> som du väljer.
  - Hanterade instanser skyddas nu med <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">inbyggd brand vägg</a>.
  - Konfigurera instanser för att använda [offentliga slut punkter](sql-database-managed-instance-public-endpoint-configure.md), en [proxy-Åsidosätt](/sql-database-connectivity-architecture.md#connection-policy) anslutning för att få bättre nätverks prestanda, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuella kärnor på Gen5-maskinvarukompatibilitet</a> eller <a href="https://aka.ms/managed-instance-configurable-backup-retention">Konfigurera kvarhållning av säkerhets kopior upp till 35 dagar</a> för återställning vid tidpunkter. Långsiktig kvarhållning av säkerhets kopior (upp till 10 år) är fortfarande inte aktiverat så att du kan använda kopierings <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">bara säkerhets kopior</a> som ett alternativ.
  - Med nya funktioner kan du <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geo-återställa databasen till ett annat data Center med hjälp av PowerShell</a>, [byta namn på databas](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [ta bort virtuellt kluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Den nya inbyggda [rollen instans deltagar roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) möjliggör separering av tull (SOD) efterlevnad av säkerhets principer och efterlevnad av företags standarder.
  - Hanterad instans är tillgänglig i följande Azure Government regioner till GA (US Gov, Texas, US Gov, Arizona) och i Kina, norra 2 och Kina, östra 2. Den är också tillgänglig i följande offentliga regioner: Australien, centrala, Australien, centrala 2, södra Brasilien, västra Frankrike, Förenade Arabemiraten Central, Förenade Arabemiraten Nord, Sydafrika, norra, södra Afrika.

## <a name="fixed-known-issues"></a>Fasta kända problem

- **Aug 2019** -inneslutna databaser stöds fullt ut i den hanterade instansen.

## <a name="updates"></a>Uppdateringar

En lista över SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database).

Uppdateringar och förbättringar av alla Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Bidra till innehåll

Information om hur du bidrar till Azure SQL Database-dokumentationen finns i [Guide för dokument bidrag](https://docs.microsoft.com/contribute/).
