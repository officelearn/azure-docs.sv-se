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
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592102"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering för SSISDB-databasen. När en redundansväxling inträffar kan du se till att Azure-SSIS IR fortsätter att arbeta med den sekundära databasen.

Mer information om geo-replikering och redundans för SQL Database finns i [Översikt: aktiva grupper för geo-replikering och automatisk redundans](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR redundans med Azure SQL Database Hanterad instans

### <a name="prerequisites"></a>Krav

Azure SQL Database hanterade instansen använder **huvud nyckeln för-databasen (DMK)** för att skydda data, autentiseringsuppgifter och anslutnings information som lagras i databasen. Om du vill aktivera automatisk dekryptering av DMK krypteras en kopia av nyckeln med hjälp av **serverns huvud nyckel (SMK)**. Men SMK är inte replikerat i redundansväxlingen, så du måste lägga till ytterligare ett lösen ord på både primära och sekundära instanser för DMK dekryptering efter redundansväxlingen.

1. Kör kommandot nedan på SSISDB på den primära instansen. Det här steget lägger till ett nytt krypterings lösen ord.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Skapa redundans grupp på Azure SQL Database Hanterad instans.

3. Kör **sp_control_dbmasterkey_password** på den sekundära instansen med det nya krypterings lösen ordet.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 – Azure-SSIS IR pekar på Läs-och skriv lyssnar-slutpunkt

Om du vill Azure-SSIS IR peka på den skrivskyddade slut punkten för lyssnare måste du först peka på primär server slut punkt. När du har angett SSISDB till redundans gruppen kan du ändra till Läs-och skriv-slut punkt och starta om Azure-SSIS IR.

#### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

1. Stoppa Azure-SSIS IR på primär region.

2. Redigera Azure-SSIS IR med ny region, VNET och anpassad konfigurera SAS URI-information för sekundär instans. När Azure-SSIS IR pekar på Läs-och skriv lyssnare och slut punkten är transparent för Azure-SSIS IR behöver du inte redigera slut punkten.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 – Azure-SSIS IR pekar på primär server slut punkt

Scenariot är lämpligt om Azure-SSIS IR pekar på primär server slut punkt.

#### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

1. Stoppa Azure-SSIS IR på primär region.

2. Redigera Azure-SSIS IR med ny region, slut punkt och VNET-information för sekundär instans.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Scenario 3 – Azure-SSIS IR pekar på den offentliga slut punkten för Azure SQL Database hanterade instansen

Scenariot är lämpligt om Azure-SSIS IR pekar på den offentliga slut punkten för Azure SQL Database hanterade instansen och den inte går att ansluta till VNET. Den enda skillnaden mellan scenario 2 och det här scenariot är att du inte behöver redigera VNET-information om Azure-SSIS IR efter redundansväxlingen.

#### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

1. Stoppa Azure-SSIS IR på primär region.

2. Redigera Azure-SSIS IR med ny region och slut punkts information för sekundär instans.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 4 – koppla en befintlig SSISDB (SSIS Catalog) till en ny Azure-SSIS IR

Det här scenariot är lämpligt om du vill etablera en ny Azure-SSIS IR på en annan region eller om du vill att din SSISDB ska fortsätta att arbeta med en ny Azure-SSIS IR i en ny region när en ADF-eller Azure-SSIS IR-katastrof inträffar i den aktuella regionen.

#### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

> [!NOTE]
> Steg 4 (generering av IR) måste göras via PowerShell. Azure Portal kommer att rapportera ett fel som anger att SSISDB redan finns.

1. Stoppa Azure-SSIS IR på primär region.

2. Kör den lagrade proceduren för att uppdatera metadata i SSISDB för att godkänna anslutningar från ** \<new_data_factory_name\> ** och ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Skapa en ny data fabrik med ** \<namnet\> new_data_factory_name** i den nya regionen. Mer information finns i skapa en data fabrik.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Mer information om PowerShell-kommandot finns i [skapa en Azure-datafabrik med PowerShell](quickstart-create-data-factory-powershell.md)

4. Skapa en ny Azure-SSIS IR med ** \<namnet\> new_integration_runtime_name** i den nya regionen med Azure PowerShell.

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



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR redundans med Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 – Azure-SSIS IR pekar på Läs-och skriv lyssnar-slutpunkt

Det här scenariot är lämpligt Azure-SSIS IR som pekar på slut punkten för Läs-och skriv lyssnare i gruppen för växling vid fel och SQL Database-servern *inte* har kon figurer ATS med den virtuella nätverks tjänstens slut punkts regel. Om du vill Azure-SSIS IR peka på den skrivskyddade slut punkten för lyssnare måste du först peka på primär server slut punkt. När du har angett SSISDB till redundans gruppen kan du ändra till Läs-och skriv-slut punkt och starta om Azure-SSIS IR.

#### <a name="solution"></a>Lösning

När redundansväxlingen inträffar är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primära gruppen för redundans. Om du vill uppdatera regionen eller annan information i Azure-SSIS IR kan du stoppa den, redigera och starta om.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 – Azure-SSIS IR pekar på primär server slut punkt

Scenariot är lämpligt om Azure-SSIS IR pekar på primär server slut punkt.

#### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

1. Stoppa Azure-SSIS IR på primär region.

2. Redigera Azure-SSIS IR med ny region, slut punkt och VNET-information för sekundär instans.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3 – koppla en befintlig SSISDB (SSIS Catalog) till en ny Azure-SSIS IR

Det här scenariot är lämpligt om du vill etablera en ny Azure-SSIS IR på en annan region eller om du vill att din SSISDB ska fortsätta att arbeta med en ny Azure-SSIS IR i en ny region när en ADF-eller Azure-SSIS IR-katastrof inträffar i den aktuella regionen.

#### <a name="solution"></a>Lösning

> [!NOTE]
> Steg 4 (generering av IR) måste göras via PowerShell. Azure Portal kommer att rapportera ett fel som anger att SSISDB redan finns.

1. Stoppa Azure-SSIS IR på primär region.

2. Kör den lagrade proceduren för att uppdatera metadata i SSISDB för att godkänna anslutningar från ** \<new_data_factory_name\> ** och ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Skapa en ny data fabrik med ** \<namnet\> new_data_factory_name** i den nya regionen. Mer information finns i skapa en data fabrik.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Mer information om PowerShell-kommandot finns i [skapa en Azure-datafabrik med PowerShell](quickstart-create-data-factory-powershell.md)

4. Skapa en ny Azure-SSIS IR med ** \<namnet\> new_integration_runtime_name** i den nya regionen med Azure PowerShell.

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


## <a name="next-steps"></a>Nästa steg

Överväg följande konfigurations alternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS Integration Runtime för hög prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa konfigurationen av Azure SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
