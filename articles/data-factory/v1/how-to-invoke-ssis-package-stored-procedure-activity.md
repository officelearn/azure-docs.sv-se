---
title: "Anropa SSIS-paket med hjälp av Azure Data Factory - lagrade Proceduraktiviteten | Microsoft Docs"
description: "Den här artikeln beskriver hur du anropa ett SQL Server Integration Services (SSIS)-paket från ett Azure Data Factory-pipelinen med hjälp av den lagrade Proceduraktiviteten."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: ff0d1e5d644926864641ceb3e3c3a102167c1fd2
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Anropa ett SSIS-paket med hjälp av aktiviteten lagrad procedur i Azure Data Factory
Den här artikeln beskriver hur du anropa ett SSIS-paket från ett Azure Data Factory-pipelinen genom att använda en lagrad procedur-aktivitet. 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory som är allmänt tillgänglig. Om du använder version 2 av Data Factory-tjänsten, som är tillgänglig som förhandsversion, se [anropa SSIS-paket med hjälp av aktiviteten lagrad procedur i version 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Krav

### <a name="azure-sql-database"></a>Azure SQL Database 
Den här genomgången i den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en Azure SQL hanteras-instans (privat förhandsvisning).

### <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS-integrering körning om du inte har någon av följande steg för steg-anvisningarna i den [genomgång: distribuera SSIS-paket](../tutorial-deploy-ssis-packages-azure.md). Du måste skapa en datafabrik version 2 för att skapa en Azure-SSIS-integrering körning. 

### <a name="azure-powershell"></a>Azure PowerShell
Installera de senaste Azure PowerShell-modulerna genom att följa instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Följande procedur innehåller steg för att skapa en datafabrik. Du kan skapa en pipeline för med en lagrad procedur aktivitet i denna data factory. Aktiviteten lagrad procedur kör en lagrad procedur i SSIDB-databasen för att köra SSIS-paket.

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen
2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. För att skapa datafabriken, kör du följande **ny AzureRmDataFactory** cmdlet, med hjälp av egenskapen plats och ResourceGroupName från $ResGrp-variabeln: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    $df 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* För närvarande kan du endast skapa datafabriker i Data Factory version 2 i regionerna USA, östra, USA östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst om du vill länka din Azure SQL-databas som är värd för SSIS-katalog till din data factory. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSIDB-databasen och kör en lagrad procedur om du vill köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i **C:\ADF\RunSSISPackage** mappen med följande innehåll: (skapa mappen ADFv2TutorialBulkCopy om den inte redan finns.)

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; och &lt;password&gt; med värdena för din Azure SQL Database innan du sparar filen.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<AZURE SQL SERVER NAME>.database.windows.net,1433;Database=SSISDB;User ID=<USERNAME>;Password=<PASSWORD>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. I **Azure PowerShell**, växla till den **C:\ADF\RunSSISPackage** mapp.
3. Kör den **ny AzureRmDataFactoryLinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Denna datamängd för utdata är en dummy datamängd som schemat för pipeline-enheter. Observera att frekvensen är inställd på timme och intervall har angetts till 1. Därför körs pipelinen när en timme i pipeline- och sluttider. 

1. Skapa en OuputDataset.json-fil med följande innehåll: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Kör den **ny AzureRmDataFactoryDataset** för att skapa en datamängd. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med aktiviteten lagrad procedur 
I det här steget skapar du en pipeline med en lagrad procedur-aktivitet. Aktiviteten anropar sp_executesql lagrade proceduren för att köra SSIS-paket. 

1. Skapa en JSON-fil med namnet **MyPipeline.json** i den **C:\ADF\RunSSISPackage** mappen med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;MAPPNAMN&gt;, &lt;PROJEKTNAMN&gt;, &lt;PAKETNAMNET&gt; med namn på mappen, projekt och paketet i katalogen SSIS innan du sparar filen. 

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'SsisAdfTest', @project_name=N'MyProject', @package_name=N'Package.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Att skapa pipelinen: **RunSSISPackagePipeline**kör den **Set AzureRmDataFactoryPipeline** cmdlet.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning
Använd den **Invoke-AzureRmDataFactoryPipeline** för att köra pipelinen. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

```powershell
$RunId = New-AzureRmDataFactoryPipeline $df -File ".\MyPipeline.json"
```

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

2. Kör **Get-AzureRmDataFactorySlice** att få information om alla segment i den utgående dataset **, vilket är utdatatabellen för pipelinen.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. 
3. Kör **Get-AzureRmDataFactoryRun** för att hämta information om aktiviteten som körs för en viss sektor.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. När sektorn har statusen Klar, kontrollerar du mappen **partitioneddata** i behållaren **adfgetstarted** i blobblagringen för utdatan.  Det kan ta lite längre tid att skapa ett HDInsight-kluster på begäran.

    Du kan köra följande fråga mot SSIDB-databasen i din Azure SQL-server för att kontrollera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om aktiviteten lagrad procedur, finns det [lagrade proceduraktiviteten](data-factory-stored-proc-activity.md) artikel.

