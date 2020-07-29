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
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116318"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS migrering med Azure SQL Managed instance som databasens arbets belastnings mål

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du migrerar databas arbets belastningar från en SQL Server instans till en Azure SQL-hanterad instans, bör du vara bekant med [Azure Data Migration service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) och [nätverkstopologier för migrering av SQL-hanterade instanser med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Den här artikeln fokuserar på migreringen av SSIS-paket (SQL Server integration service) som lagras i SSIS Catalog (SSISDB) och SQL Server Agent jobb som schemalägger körning av SSIS-paket.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrera SSIS-katalogen (SSISDB)

SSISDB-migrering kan göras med hjälp av DMS, enligt beskrivningen i artikeln: [MIGRERA SSIS-paket till SQL-hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS-jobb till SQL-hanterad instans agent

SQL-hanterad instans har en inbyggd schemaläggare i första klass, precis som SQL Server Agent lokalt.  Eftersom ett Migreringsverktyg för SSIS-jobb inte är tillgängligt än, måste de migreras från SQL Server Agent lokalt till SQL-hanterad instans agent via skript/manuell kopiering.

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Nätverkstopologier för migrering av SQL-hanterade instanser med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrera SSIS-paket till en SQL-hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Nästa steg

- [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Köra SSIS-paket som distribuerats i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
