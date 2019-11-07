---
title: SSIS migrering med Azure SQL Database Hanterad instans som databasens arbets belastnings mål
description: SSIS-migrering med Azure SQL Database hanterade instansen som databasens arbets belastnings mål.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6ea9134085812b99d59ad64aa2c9ec1b2ff827d9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684394"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS migrering med Azure SQL Database Hanterad instans som databasens arbets belastnings mål

När du migrerar databas arbets belastningar från SQL Server lokalt till Azure SQL Database Hanterad instans bör du vara bekant med [Azure Data Migration service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) och [nätverkstopologier för Azure SQL Database hanterade instanser av migrering använda DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Den här artikeln fokuserar på migreringen av SSIS-paket (SQL Server integration service) som lagras i SSIS Catalog (SSISDB) och SQL Server Agent jobb som schemalägger körning av SSIS-paket.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrera SSIS-katalogen (SSISDB)

SSISDB-migrering kan göras med hjälp av DMS, enligt beskrivningen i artikeln: [MIGRERA SSIS-paket till en Azure SQL Database Hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS-jobb till Azure SQL Database Hanterad instans agent

Azure SQL Database hanterade instansen har en inbyggd schemaläggare i första klass, precis som SQL Server Agent lokalt.  Eftersom ett Migreringsverktyg för SSIS-jobb inte är tillgängligt än, måste de migreras från SQL Server Agent lokalt till Azure SQL Database Hanterad instans agent via skript/manuell kopiering.

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Nätverkstopologier för Azure SQL Database hanterade instans-migreringar med DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrera SSIS-paket till en Azure SQL Database Hanterad instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Nästa steg

- [Ansluta till SSISDB i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Köra SSIS-paket som distribuerats i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
