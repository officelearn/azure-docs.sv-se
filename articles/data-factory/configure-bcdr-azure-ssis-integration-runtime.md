---
title: Konfigurera Azure-SSIS Integration Runtime för SQL Database redundans
description: Den här artikeln beskriver hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans för SSISDB-databasen
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928504"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans

Den här artikeln beskriver hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering för SSISDB-databasen. När en redundansväxling inträffar kan du se till att Azure-SSIS IR fortsätter att arbeta med den sekundära databasen.

Mer information om geo-replikering och redundans för SQL Database finns i [Översikt: aktiva grupper för geo-replikering och automatisk redundans](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 – Azure-SSIS IR pekar på Läs-och skriv lyssnar-slutpunkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller när följande villkor är uppfyllda:

- Azure-SSIS IR pekar på den Läs-och skriv lyssnar-slut punkten för gruppen redundans.

  AND

- SQL Database servern har *inte* kon figurer ATS med den virtuella nätverks tjänstens slut punkts regel.

### <a name="solution"></a>Lösning

När redundansväxlingen inträffar är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primära gruppen för redundans.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 – Azure-SSIS IR pekar på primär server slut punkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller när något av följande villkor är uppfyllt:

- Azure-SSIS IR pekar på den primära server slut punkten för gruppen för redundans. Den här slut punkten ändras när redundans inträffar.

  ELLER

- Azure SQL Database servern har kon figurer ATS med den virtuella nätverks tjänstens slut punkts regel.

  ELLER

- Databas servern är en SQL Database Hanterad instans som kon figurer ATS med ett virtuellt nätverk.

### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

1. Stoppa Azure-SSIS IR.

2. Konfigurera om IR-filen så att den pekar mot den nya primära slut punkten och till ett virtuellt nätverk i den nya regionen.

3. Starta om IR.

I följande avsnitt beskrivs de här stegen i detalj.

### <a name="prerequisites"></a>Krav

- Kontrol lera att du har aktiverat haveri beredskap för din Azure SQL Database-Server om servern har ett avbrott på samma tid. Mer information finns i [Översikt över affärs kontinuitet med Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Om du använder ett virtuellt nätverk i den aktuella regionen måste du använda ett annat virtuellt nätverk i den nya regionen för att ansluta Azure-SSIS integration Runtime. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för BLOB-behållaren som lagrar det anpassade installations skriptet och tillhör ande filer, så att den fortsätter att vara tillgänglig under ett avbrott. Mer information finns i [Konfigurera en anpassad installation på en Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att stoppa Azure-SSIS IR, växla IR till en ny region och starta den igen.

1. Stoppa IR i den ursprungliga regionen.

2. Anropa följande kommando i PowerShell för att uppdatera IR med de nya inställningarna.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Mer information om PowerShell-kommandot finns i [Skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Starta IR igen.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3 – koppla en befintlig SSISDB (SSIS Catalog) till en ny Azure-SSIS IR

När en ADF-eller Azure-SSIS IR-haverining sker i den aktuella regionen kan du göra så att din SSISDB fungerar med en ny Azure-SSIS IR i en ny region.

### <a name="prerequisites"></a>Krav

- Om du använder ett virtuellt nätverk i den aktuella regionen måste du använda ett annat virtuellt nätverk i den nya regionen för att ansluta Azure-SSIS integration Runtime. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för BLOB-behållaren som lagrar det anpassade installations skriptet och tillhör ande filer, så att den fortsätter att vara tillgänglig under ett avbrott. Mer information finns i [Konfigurera en anpassad installation på en Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att stoppa Azure-SSIS IR, växla IR till en ny region och starta den igen.

1. Kör den lagrade proceduren för att göra SSISDB kopplade till **\<new_data_factory_name\>** eller **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Skapa en ny data fabrik med namnet **\<new_data_factory_name\>** i den nya regionen. Mer information finns i skapa en data fabrik.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Mer information om PowerShell-kommandot finns i [skapa en Azure-datafabrik med PowerShell](quickstart-create-data-factory-powershell.md)

3. Skapa en ny Azure-SSIS IR med namnet **\<new_integration_runtime_name\>** i den nya regionen med Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Mer information om PowerShell-kommandot finns i [Skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md)

4. Starta IR igen.

## <a name="next-steps"></a>Nästa steg

Överväg följande konfigurations alternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS Integration Runtime för hög prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa konfigurationen av Azure SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
