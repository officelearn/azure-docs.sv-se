---
title: SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål
description: SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål.
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
ms.openlocfilehash: 6de08faee78deeb86117084b420eb5043153f62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88186054"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du migrerar databas arbets belastningar från en SQL Server instans till en Azure SQL-hanterad instans, bör du vara bekant med [Azure Data Migration service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) och [nätverkstopologier för migrering av SQL-hanterade instanser med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Den här artikeln fokuserar på migreringen av SSIS-paket (SQL Server integration service) som lagras i SSIS Catalog (SSISDB) och SQL Server Agent jobb som schemalägger körning av SSIS-paket.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrera SSIS-katalogen (SSISDB)

SSISDB-migrering kan göras med hjälp av DMS, enligt beskrivningen i artikeln: [MIGRERA SSIS-paket till SQL-hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS-jobb till SQL-hanterad instans agent

SQL-hanterad instans har en inbyggd schemaläggare i första klass, precis som SQL Server Agent lokalt.  Du kan [köra SSIS-paket via Azure SQL-hanterad instans agent](how-to-invoke-ssis-package-managed-instance-agent.md).

Eftersom ett Migreringsverktyg för SSIS-jobb inte är tillgängligt än, måste de migreras från SQL Server Agent lokalt till SQL-hanterad instans agent via skript/manuell kopiering.

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Nätverkstopologier för migrering av SQL-hanterade instanser med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrera SSIS-paket till en SQL-hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Nästa steg

- [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Köra SSIS-paket som distribuerats i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
