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
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188730"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering för SSISDB-databasen. När en redundansväxling inträffar kan du se till att Azure-SSIS IR fortsätter att arbeta med den sekundära databasen.

Mer information om geo-replikering och redundans för SQL Database finns i [Översikt: aktiva grupper för geo-replikering och automatisk redundans](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR redundans med Azure SQL Database Hanterad instans

### <a name="prerequisites"></a>Krav
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

### <a name="solution"></a>Lösning
Om en redundansväxling inträffar, om du vill använda befintliga Azure-SSIS IR på den primära regionen:
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

4. Ändra Server namnet i **ConnectionManager** för dina SSIS-paket med namnet på den sekundära instans servern och distribuera sedan om paketen och kör.

Om du vill etablera en ny Azure-SSIS IR i den sekundära regionen:
> [!NOTE]
> Steg 4 (generering av IR) måste göras via PowerShell. Azure Portal kommer att rapportera ett fel som anger att SSISDB redan finns.
1. Stoppa Azure-SSIS IR på primär region.

2. Kör den lagrade proceduren för att uppdatera metadata i SSISDB för att godkänna anslutningar från ** \<new_data_factory_name\> ** och ** \<new_integration_runtime_name\>**.
   
```SQL
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

5. Ändra Server namnet i **ConnectionManager** för dina SSIS-paket med namnet på den sekundära instans servern och distribuera sedan om paketen och kör.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR redundans med Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 – Azure-SSIS IR pekar på Läs-och skriv lyssnar-slutpunkt

#### <a name="conditions"></a>Villkor

Det här avsnittet gäller när följande villkor är uppfyllda:

- Azure-SSIS IR pekar på den Läs-och skriv lyssnar-slut punkten för gruppen redundans.

  AND

- SQL Database servern har *inte* kon figurer ATS med den virtuella nätverks tjänstens slut punkts regel.

#### <a name="solution"></a>Lösning

När redundansväxlingen inträffar är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primära gruppen för redundans.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 – Azure-SSIS IR pekar på primär server slut punkt

#### <a name="conditions"></a>Villkor

Det här avsnittet gäller när något av följande villkor är uppfyllt:

- Azure-SSIS IR pekar på den primära server slut punkten för gruppen för redundans. Den här slut punkten ändras när redundans inträffar.

  ELLER

- Azure SQL Database servern har kon figurer ATS med den virtuella nätverks tjänstens slut punkts regel.


#### <a name="solution"></a>Lösning

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

4. Ändra Server namnet i **ConnectionManager** för dina SSIS-paket med namnet på den sekundära instans servern och distribuera sedan om paketen och kör.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3 – koppla en befintlig SSISDB (SSIS Catalog) till en ny Azure-SSIS IR

När en ADF-eller Azure-SSIS IR-haverining sker i den aktuella regionen kan du göra så att din SSISDB fungerar med en ny Azure-SSIS IR i en ny region.

#### <a name="solution"></a>Lösning

> [!NOTE]
> Steg 4 (generering av IR) måste göras via PowerShell. Azure Portal kommer att rapportera ett fel som anger att SSISDB redan finns.

1. Stoppa Azure-SSIS IR på primär region.

2. Kör den lagrade proceduren för att uppdatera metadata i SSISDB för att godkänna anslutningar från ** \<new_data_factory_name\> ** och ** \<new_integration_runtime_name\>**.
   
```SQL
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

5. Ändra Server namnet i **ConnectionManager** för dina SSIS-paket med namnet på den sekundära instans servern och distribuera sedan om paketen och kör.


## <a name="next-steps"></a>Nästa steg

Överväg följande konfigurations alternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS Integration Runtime för hög prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa konfigurationen av Azure SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
