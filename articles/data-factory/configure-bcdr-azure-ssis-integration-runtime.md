---
title: Konfigurera Azure-SSIS Integration Runtime för redundans för SQL-databas | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans för SSIDB-databasen
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2012ccf4d9fd3e62ba248f29f922f868077e4061
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42061521"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans

Den här artikeln beskriver hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering för SSIDB-databasen. När det uppstår redundans, kan du se till att Azure-SSIS IR ser till att arbeta med den sekundära databasen.

Mer information om geo-replikering och redundans för SQL Database finns i [översikt: aktiv geo-replikering och automatisk redundans grupper](../sql-database/sql-database-geo-replication-overview.md).

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 – Azure-SSIS IR pekar du skrivskyddad lyssnarslutpunkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller när följande villkor är uppfyllda:

- Azure-SSIS IR pekar på skrivskyddad lyssnarslutpunkt av redundansgruppen.

  OCH

- SQL Database-servern är *inte* konfigurerats med slutpunkt-regel för virtuella nätverk-tjänsten.

### <a name="solution"></a>Lösning

När redundansväxlingen sker, är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primärt av redundansgruppen.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 – Azure-SSIS IR pekar på primär server-slutpunkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller om någon av följande villkor föreligger:

- Azure-SSIS IR pekar på den primära Serverslutpunkten för redundansgruppen. Den här slutpunkten ändras vid redundans.

  ELLER

- Azure SQL Database-servern har konfigurerats med slutpunkt-regel för virtuella nätverk-tjänsten.

  ELLER

- Databasservern är en SQL Database Managed Instance konfigurerats med ett virtuellt nätverk.

### <a name="solution"></a>Lösning

Vid redundans som du behöver göra följande:

1. Stoppa Azure-SSIS IR.

2. Konfigurera om IR så att den pekar till den nya primära slutpunkten och till ett virtuellt nätverk i det nya området.

3. Starta om i IR.

I följande avsnitt beskrivs de här stegen i detalj.

### <a name="prerequisites"></a>Förutsättningar

- Kontrollera att du har aktiverat haveriberedskap för Azure SQL Database-servern om servern har ett avbrott på samma gång. Mer information finns i [översikt över affärskontinuitet med Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Om du använder ett virtuellt nätverk i den aktuella regionen, som du behöver använda ett annat virtuellt nätverk i det nya området för att ansluta din Azure-SSIS integration runtime. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för blob-behållare som lagrar dina anpassade installationsskriptet och associerade filer, så att det fortsätter att vara tillgänglig under ett avbrott. Mer information finns i [konfigurera en anpassad installation på en Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att stoppa din Azure-SSIS IR, växla IR till en ny region och starta den igen.

1. Stoppa IR i den ursprungliga regionen.

2. Anropa följande kommando i PowerShell för att uppdatera IR med de nya inställningarna.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Mer information om det här PowerShell-kommandot finns i [skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Starta IR igen.

## <a name="next-steps"></a>Nästa steg

Överväg dessa andra konfigurationsalternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS Integration Runtime för höga prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa installationsprogrammet för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
