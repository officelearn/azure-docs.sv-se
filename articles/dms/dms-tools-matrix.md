---
title: Matris för Azure Database Migration Service verktyg
description: Lär dig mer om tjänsterna och verktygen som är tillgängliga för att migrera databaser och stöd för olika faser av migreringsprocessen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/03/2020
ms.openlocfilehash: de1e00d92661c3768efa49e5809751bf4f2327d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84267876"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Tillgängliga tjänster och verktyg för datamigreringsscenarier

Den här artikeln innehåller en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter.

Följande tabeller identifierar de tjänster och verktyg som du kan använda för att planera för datamigrering och för att slutföra de olika faserna.

> [!NOTE]
> I följande tabeller representerar objekt som är markerade med en asterisk (*) verktyg från tredje part.

## <a name="business-justification-phase"></a>Affärs justerings fas

| Källa | Mål | Undersöka<br/>Lager | Mål och SKU<br/>rekommenderade | TCO/ROI och<br/>Affärs ärende |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-databas | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Azure SQL DB-MI | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Virtuell Azure SQL-dator | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS-SQL | Azure SQL DB, MI, VM |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, MI, VM | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server | [Kart verktyg](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB för MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB för MySQL |  |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |  | [TCO-kalkylator](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Fas för migrering

| Källa | Mål | Åtkomst till AppData<br/>Skikt utvärdering | Databas<br/>Utvärdering | Prestanda<br/>Utvärdering |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-databas | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB-MI | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Virtuell Azure SQL-dator | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| RDS-SQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DIETANOLAMIN](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | SQL DW | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB för MySQL |  |  |  |
| RDS MySQL | Azure DB för MySQL |  |  |  |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |  |  |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |  |  |
| DB2 | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Azure SQL DB, MI, VM |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Migrerings fas

| Källa | Mål | Schema | Data<br/>Anslutningen | Data<br/>Onlinemallar |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-databas | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB-MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Virtuell Azure SQL-dator | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW |  |  |  |
| RDS-SQL | Azure SQL DB, MI, VM | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Inspirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server | [Inspirer](https://www.ispirer.com/solutions) | [Inspirer](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis-data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis-data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis-data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis-data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis-data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis-data *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB för MySQL | [MySQL-dump *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB för MySQL | [MySQL-dump *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server | [Dumpning av PG *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server | [Dumpning av PG *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Inspirer](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Striims](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM | [Inspirer](https://www.ispirer.com/solutions) | [Inspirer](https://www.ispirer.com/solutions) | |
| | | | | |

## <a name="post-migration-phase"></a>Fasen efter migreringen

| Källa | Mål | Optimera |
| --- | --- | --- |
| SQL Server | Azure SQL-databas | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB-MI | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Virtuell Azure SQL-dator | [Molnbaserad Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |
| RDS-SQL | Azure SQL DB, MI, VM |  |
| Oracle | Azure SQL DB, MI, VM |  |
| Oracle | SQL DW |  |
| Oracle | Azure DB för PostgreSQL –<br/>Enskild server |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, MI, VM |  |
| MySQL | Azure DB för MySQL |  |
| RDS MySQL | Azure DB för MySQL |  |
| PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |
| RDS-PostgreSQL | Azure DB för PostgreSQL –<br/>Enskild server |  |
| DB2 | Azure SQL DB, MI, VM |  |
| Access | Azure SQL DB, MI, VM |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM |  |
| Sybase-SAP-SWEETIQ | Azure SQL DB, MI, VM |  |
| | | |

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service](dms-overview.md).
