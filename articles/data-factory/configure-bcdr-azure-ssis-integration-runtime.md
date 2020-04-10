---
title: Konfigurera Azure-SSIS-integreringskörning för fel vid sql-databas
description: I den hÃ¤r artikeln beskrivs sÃ¤jligare DÃ¤r du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replication och redundansã¤r SSISDB-databasen
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
ms.openlocfilehash: 532258cecd823e10057ddc3536cd24071e444581
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992070"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS Integration Runtime med Azure SQL Database geo-replication och redundans

I den här artikeln beskrivs hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replication för SSISDB-databasen. När en redundans inträffar kan du se till att Azure-SSIS IR fortsätter att arbeta med den sekundära databasen.

Mer information om georeplikering och redundans för SQL Database finns i [Översikt: Aktiva geo-replikerings- och automatisk redundansgrupper](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 - Azure-SSIS IR pekar på slutpunkt för läs-skrivavlyssnare

### <a name="conditions"></a>Villkor

Det här avsnittet gäller när följande villkor är uppfyllda:

- Azure-SSIS IR pekar på slutpunkten för läs-skriv-lyssnaren i redundansgruppen.

  AND

- SQL Database-servern är *inte* konfigurerad med slutpunktsregeln för den virtuella nätverkstjänsten.

### <a name="solution"></a>Lösning

När redundans inträffar är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primärheten i redundansgruppen.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 - Azure-SSIS IR pekar på primär serverslutpunkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller när något av följande villkor är sant:

- Azure-SSIS IR pekar på den primära serverslutpunkten för redundansgruppen. Den här slutpunkten ändras när redundans sker.

  ELLER

- Azure SQL Database-servern är konfigurerad med slutpunktsregeln för den virtuella nätverkstjänsten.

  ELLER

- Databasservern är en SQL Database Managed Instance som konfigurerats med ett virtuellt nätverk.

### <a name="solution"></a>Lösning

När redundans inträffar måste du göra följande:

1. Stoppa Azure-SSIS IR.

2. Konfigurera om IR:et så att den pekar på den nya primära slutpunkten och till ett virtuellt nätverk i den nya regionen.

3. Starta om IR.Restart the IR.

I följande avsnitt beskrivs dessa steg mer i detalj.

### <a name="prerequisites"></a>Krav

- Kontrollera att du har aktiverat haveriberedskap för din Azure SQL Database-server om servern har ett avbrott samtidigt. Mer information finns i [Översikt över affärskontinuitet med Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Om du använder ett virtuellt nätverk i den aktuella regionen måste du använda ett annat virtuellt nätverk i den nya regionen för att ansluta din Azure-SSIS-integreringskörning. Mer information finns i [Ansluta till en Azure-SSIS-integreringskörning till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för blob-behållaren som lagrar ditt anpassade inställningsskript och associerade filer, så det fortsätter att vara tillgängligt under ett avbrott. Mer information finns i [Konfigurera en anpassad installation på en Azure-SSIS-integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att stoppa din Azure-SSIS IR, växla IR till en ny region och starta den igen.

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

    Mer information om det här [PowerShell-kommandot finns i Skapa Azure-SSIS-integreringskörningen i Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Starta IR igen.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3 - Koppla en befintlig SSISDB (SSIS-katalog) till en ny Azure-SSIS IR

När en ADF- eller Azure-SSIS IR-katastrof inträffar i den aktuella regionen kan du få din SSISDB att fortsätta arbeta med en ny Azure-SSIS IR i en ny region.

### <a name="prerequisites"></a>Krav

- Om du använder ett virtuellt nätverk i den aktuella regionen måste du använda ett annat virtuellt nätverk i den nya regionen för att ansluta din Azure-SSIS-integreringskörning. Mer information finns i [Ansluta till en Azure-SSIS-integreringskörning till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för blob-behållaren som lagrar ditt anpassade inställningsskript och associerade filer, så det fortsätter att vara tillgängligt under ett avbrott. Mer information finns i [Konfigurera en anpassad installation på en Azure-SSIS-integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att flytta din Azure-SSIS IR till en ny region.
> [!NOTE]
> Steg 3 (skapandet av IR) måste göras via PowerShell. Azure-portalen rapporterar ett fel som anger att SSISDB redan finns.

1. Kör lagrad procedur för att uppdatera metadata i SSISDB för att acceptera anslutningar från ** \<new_data_factory_name\> ** och ** \<new_integration_runtime_name\>**.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Skapa en ny ** \<\> ** datafabrik med namnet new_data_factory_name i den nya regionen. Mer information finns i Skapa en datafabrik.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Mer information om det här PowerShell-kommandot finns i [Skapa en Azure-datafabrik med PowerShell](quickstart-create-data-factory-powershell.md)

3. Skapa en ny Azure-SSIS ** \<\> ** IR med namnet new_integration_runtime_name i den nya regionen med Azure PowerShell.

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

    Mer information om det här [PowerShell-kommandot finns i Skapa Azure-SSIS-integreringskörningen i Azure Data Factory](create-azure-ssis-integration-runtime.md)

4. Starta IR igen.

## <a name="next-steps"></a>Nästa steg

Tänk på dessa andra konfigurationsalternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS-integrationskörningen för hög prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa konfigurationen av Azure SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
