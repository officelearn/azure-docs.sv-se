---
title: Migrera lokala SSIS-arbetsbelastningar till SSIS i Azure Data Factory
description: Migrera lokala SSIS-arbetsbelastningar till SSIS i ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929796"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrera lokala SSIS-arbetsbelastningar till SSIS i ADF

## <a name="overview"></a>Översikt

När du migrerar dina databasarbetsbelastningar från SQL Server lokalt till Azure-databastjänster, nämligen Azure SQL Database eller Azure SQL Database hanterad instans, är dina ETL-arbetsbelastningar på SQL Server Integration Services (SSIS) som ett av de primära mervärdet tjänster kommer också att behöva migreras.

Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) stöder körning av SSIS-paket. När Azure-SSIS IR har etablerats kan du sedan använda välbekanta verktyg, till exempel SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) och kommandoradsverktyg, till exempel dtinstall/dtutil/dtexec, för att distribuera och köra dina paket i Azure. Mer information finns i [Översikt över Azure SSIS-lyft och skift](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

I den här artikeln beskrivs migreringsprocessen för dina ETL-arbetsbelastningar från lokala SSIS till SSIS i ADF. Migreringsprocessen består av två faser: **Bedömning** och **migrering**.

## <a name="assessment"></a>Utvärdering

För att upprätta en fullständig migreringsplan kommer en grundlig utvärdering att hjälpa till att identifiera problem med käll-SSIS-paket som skulle förhindra en lyckad migrering.

DataMigreringsassistenten (DMA) är ett fritt nedladdningsbart verktyg för detta ändamål som kan installeras och köras lokalt. DMA-utvärderingsprojekt av typen **Integrationstjänster** kan skapas för att bedöma SSIS-paket i batchar och identifiera kompatibilitetsproblem som presenteras i följande kategorier:

- Migreringsblockerare: Det här är kompatibilitetsproblem som blockerar migreringskällapaketen för att köras på Azure-SSIS IR. DMA ger vägledning som hjälper dig att lösa dessa problem.

- Informativa problem: Dessa stöds delvis eller inaktuella funktioner som används i källpaket. DMA innehåller en omfattande uppsättning rekommendationer, alternativa metoder som är tillgängliga i Azure och förmildrande åtgärder för att lösa.

### <a name="four-storage-types-for-ssis-packages"></a>Fyra lagringstyper för SSIS-paket

- SSIS-katalog (SSISDB). Detta introducerades med SQL Server 2012 och innehåller en uppsättning lagrade procedurer, vyer och tabellvärderade funktioner som används för att arbeta med SSIS-projekt/paket.
- Filsystemet.
- SQL Server-systemdatabas (MSDB).
- SSIS-paketbutik. Detta är ett pakethanteringslager ovanpå två undertyper:
  - MSDB, som är en systemdatabas i SQL Server som används för att lagra SSIS-paket.
  - Hanterat filsystem, som är en specifik mapp i SQL Server-installationssökvägen som används för att lagra SSIS-paket.

DMA stöder för närvarande batch-bedömning av paket som lagras i **Filsystem,** **Package Store**och **SSIS katalog** sedan **DMA version v5.0**.

Skaffa [DMA](https://docs.microsoft.com/sql/dma/dma-overview)och [utför din paketutvärdering med den.](https://docs.microsoft.com/sql/dma/dma-assess-ssis)

## <a name="migration"></a>Migrering

Beroende på [lagringstyperna](#four-storage-types-for-ssis-packages) av käll-SSIS-paket och migreringsmålet för databasarbetsbelastningar kan stegen för att migrera **SSIS-paket** och **SQL Server Agent-jobb** som schemalägger SSIS-paketkörningar variera. Det finns två scenarier:

- [**Azure SQL Database hanterad instans** som mål för databasarbetsbelastning](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** som mål för databasarbetsbelastning](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL Database hanterad instans** som mål för databasarbetsbelastning

| **Typ av paketlagring** |Så här batch-migreraR SSIS-paket|Så här batch-migrerar SSIS-jobb|
|-|-|-|
|SSISDB (SSISDB)|[Migrera **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Migrera SSIS-jobb till Azure SQL Database-hanterad instansagent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Filsystem|Distribuera om dem till filresurser/Azure-filer via dtinstall/dtutil/manuell kopia, eller för att hålla i filsystem för åtkomst via VNet/Self-Hosted IR. Mer information finns i [verktyget dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportera dem till filsystem/filresurser/Azure-filer via SSMS/dtutil. Mer information finns i [Exportera SSIS-paket](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paketbutik|Exportera dem till filsystem/filresurser/Azure-filer via SSMS/dtutil eller distribuera om dem till filresurser/Azure-filer via dtinstall/dtutil/manuell kopia eller förvara dem i filsystem för åtkomst via VNet/Self-Hosted IR. Mer information finns i verktyget dtutil. Mer information finns i [verktyget dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** som mål för databasarbetsbelastning

| **Typ av paketlagring** |Så här batch-migreraR SSIS-paket|Så här batch-migrerar du jobb|
|-|-|-|
|SSISDB (SSISDB)|Distribuera om till Azure-SSISDB via SSDT/SSMS. Mer information finns i [Distribuera SSIS-paket i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Filsystem|Distribuera om dem till filresurser/Azure-filer via dtinstall/dtutil/manuell kopia, eller för att hålla i filsystem för åtkomst via VNet/Self-Hosted IR. Mer information finns i [verktyget dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportera dem till filsystem/filresurser/Azure-filer via SSMS/dtutil. Mer information finns i [Exportera SSIS-paket](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Paketbutik|Exportera dem till filsystem/filresurser/Azure-filer via SSMS/dtutil eller distribuera om dem till filresurser/Azure-filer via dtinstall/dtutil/manuell kopia eller förvara dem i filsystem för åtkomst via VNet/Self-Hosted IR. Mer information finns i verktyget dtutil. Mer information finns i [verktyget dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konvertera dem till ADF-pipelines/aktiviteter/utlösare via skript/SSMS/ADF-portal. Mer information finns i [SSMS-schemaläggningsfunktionen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [Lyft och flytta SSIS-arbetsbelastningar till molnet](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrera SSIS-paket till en hanterad Azure SQL Database-instans](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Distribuera om paket till Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Nästa steg

- [Verifiera SSIS-paket som distribueras till Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Kör SSIS-paket som distribueras i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Övervaka körning av Azure SSIS-integrering](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Schemalägga SSIS-paketkörningar i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
