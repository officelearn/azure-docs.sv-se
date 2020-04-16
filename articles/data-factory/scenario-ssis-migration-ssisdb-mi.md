---
title: SSIS-migrering med Azure SQL Database-hanterad instans som mål för databasarbetsbelastning
description: SSIS-migrering med Azure SQL Database hanterad instans som mål för databasarbetsbelastning.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419059"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS-migrering med Azure SQL Database-hanterad instans som mål för databasarbetsbelastning

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du migrerar databasarbetsbelastningar från SQL Server lokalt till Azure SQL Database-hanterad instans bör du känna till [Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) och [nätverkstopologier för Azure SQL Database-hanterade instansmigreringar med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Den här artikeln fokuserar på migrering av SSIS-paket (SQL Server Integration Service) som lagras i SSIS-katalog (SSISDB) och SQL Server Agent-jobb som schemalägger SSIS-paketkörningar.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrera SSIS-katalog (SSISDB)

SSISDB-migrering kan göras med DMS, enligt beskrivningen i artikeln: [Migrera SSIS-paket till en hanterad Azure SQL-databas-hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS-jobb till Azure SQL Database-hanterad instansagent

Azure SQL Database hanterad instans har en inbyggd, förstklassig schemaläggare precis som SQL Server Agent lokalt.  Eftersom ett migreringsverktyg för SSIS-jobb ännu inte är tillgängligt måste de migreras från SQL Server Agent lokalt till Azure SQL Database-hanterad instansagent via skript/manuell kopia.

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Nätverkstopologier för hanterade instansmigreringar i Azure SQL Database med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrera SSI-paket till en hanterad Azure SQL Database-instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Nästa steg

- [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Kör SSIS-paket som distribueras i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
