---
title: Anropa SSIS-paket med Azure Data Factory - Lagrad proceduraktivitet
description: I den hÃ¤r artikeln beskrivs hur du anropar ett SSIS-paket (SQL Server Integration Services) frÃ¤r en Azure Data Factory-pipeline med hjälp av den lagrade proceduren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438792"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Anropa ett SSIS-paket med hjälp av lagrad proceduraktivitet i Azure Data Factory
I den här artikeln beskrivs hur du anropar ett SSIS-paket från en Azure Data Factory-pipeline med hjälp av en lagrad proceduraktivitet. 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser [du Anropa SSIS-paket med hjälp av lagrad proceduraktivitet i](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Krav

### <a name="azure-sql-database"></a>Azure SQL Database 
Genomgången i den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en hanterad Azure SQL-databas-instans.

### <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS-integreringskörning om du inte har en genom att följa steg-för-steg-instruktionen i [självstudien: Distribuera SSIS-paket](../tutorial-create-azure-ssis-runtime-portal.md). Du kan inte använda Data Factory version 1 för att skapa en Azure-SSIS-integreringskörning. 

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en lagrad proceduraktivitet som anropar ett SSIS-paket.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Följande procedur innehåller steg för att skapa en datafabrik. Du skapar en pipeline med en lagrad proceduraktivitet i den här datafabriken. Den lagrade proceduraktiviteten kör en lagrad procedur i SSISDB-databasen för att köra ditt SSIS-paket.

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen
2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Om du vill skapa datafabriken kör du följande **Cmdlet New-AzDataFactory** med egenskapen Location och ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst för att länka din Azure SQL-databas som är värd för SSIS-katalogen till din datafabrik. Data Factory använder information i den här länkade tjänsten för att ansluta till SSISDB-databasen och kör en lagrad procedur för att köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i mappen **C:\ADF\RunSSISPackage** med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt;&gt;servernamn,&gt;@&lt;&gt; &lt;användarnamn servernamn och lösenord&gt; med värden i Din Azure SQL Database innan du sparar filen. &lt;

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. I **Azure PowerShell**växlar du till mappen **C:\ADF\RunSSISPackage.**
3. Kör cmdleten **New-AzDataFactoryLinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Den här utdatauppsättningen är en dummy-datauppsättning som styr pipelinens schema. Observera att frekvensen är inställd på Timme och intervallet är inställt på 1. Därför körs pipelinen en gång i timmen inom pipelinens start- och sluttider. 

1. Skapa en OutputDataset.json-fil med följande innehåll: 
    
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
2. Kör cmdleten **New-AzDataFactoryDataset** för att skapa en datauppsättning. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med lagrad proceduraktivitet 
I det här steget skapar du en pipeline med en lagrad proceduraktivitet. Aktiviteten anropar den sp_executesql lagrade proceduren för att köra SSIS-paketet. 

1. Skapa en JSON-fil med namnet **MyPipeline.json** i mappen **C:\ADF\RunSSISPackage** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;&gt;mappnamn, &lt;&gt;projektnamn, &lt;paketnamn&gt; med namn på mapp, projekt och paket i SSIS-katalogen innan filen sparas.

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
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
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

2. Så här skapar du pipelinen: **RunSSISPackagePipeline**kör du cmdleten **New-AzDataFactoryPipeline.**

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör **Get-AzDataFactorySlice** för att få information om alla segment i utdatauppsättningen**, som är utdatatabellen för pipelinen.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. 
1. Kör **Get-AzDataFactoryRun** för att få information om aktivitetskörningar för ett visst segment.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. 

    Du kan köra följande fråga mot SSISDB-databasen i Din Azure SQL-server för att verifiera att paketet har körts. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om den lagrade proceduraktiviteten finns i artikeln [Lagrad proceduraktivitet.](data-factory-stored-proc-activity.md)

