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
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 66b4f068189fd17f08a6a57ed44233c04c16fff7
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Anropa ett SSIS-paket med hjälp av aktiviteten lagrad procedur i Azure Data Factory
Den här artikeln beskriver hur du anropa ett SSIS-paket från ett Azure Data Factory-pipelinen genom att använda en lagrad procedur-aktivitet. 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory som är allmänt tillgänglig. Om du använder version 2 av Data Factory-tjänsten, som är tillgänglig som förhandsversion, se [anropa SSIS-paket med hjälp av aktiviteten lagrad procedur i version 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-sql-database"></a>Azure SQL Database 
Den här genomgången i den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en Azure SQL hanteras-instans (privat förhandsvisning).

### <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS-integrering körning om du inte har någon av följande steg för steg-anvisningarna i den [genomgång: distribuera SSIS-paket](../tutorial-deploy-ssis-packages-azure.md). Du måste skapa en datafabrik version 2 för att skapa en Azure-SSIS-integrering körning. 

## <a name="azure-portal"></a>Azure Portal
I det här avsnittet använder du Azure-portalen för att skapa Data Factory-pipelinen med en lagrad procedur-aktivitet som anropar ett SSIS-paket.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Första steget är att skapa en datafabrik med hjälp av Azure portal. 

1. Navigera till [Azure-portalen](https://portal.azure.com). 
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. I den **nya datafabriken** anger **ADFTutorialDataFactory** för den **namn**. 
      
     ![Ny data factory-sida](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Ändra namnet på data factory (till exempel yournameADFTutorialDataFactory) om du ser följande fel för namnfältet. Se [Data Factory - namnregler](data-factory-naming-rules.md) artikel för namnregler för Data Factory-artefakter.

    `Data factory name ADFTutorialDataFactory is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
    Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../../azure-resource-manager/resource-group-overview.md).  
4. Välj **V1** för den **version**.
5. Välj **plats** för datafabriken. Endast de platser som stöds av Data Factory visas i den nedrullningsbara listan. Data lagras (Azure Storage, Azure SQL Database, etc.) och beräknar (HDInsight osv.) som används av datafabriken kan finnas på andra platser.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. När den har skapats visas den **Data Factory** sidan som visas i bilden.
   
    ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klicka på **författare och distribuera** rutan för att starta Data Factory-redigeraren.

    ![Data Factory-redigeraren](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst om du vill länka din Azure SQL-databas som är värd för SSIS-katalog till din data factory. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSIDB-databasen och kör en lagrad procedur om du vill köra ett SSIS-paket. 

1. I den Data Factory-redigeraren, klicka på **Nytt datalager** på menyn och klicka på **Azure SQL Database**. 

    ![Azure SQL Database-Nytt datalager >](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. I den högra rutan, gör du följande steg:

    1. Ersätt `<servername>` med namnet på din Azure SQL-server. 
    2. Ersätt `<databasename>` med **SSISDB** (namnet på katalogen SSIS-databasen). 
    3. Ersätt `<username@servername>` med namnet på den användare som har åtkomst till Azure SQL-servern. 
    4. Ersätt `<password>` med lösenordet för användaren. 
    5. Distribuera den länkade tjänsten genom att klicka på den **distribuera** i verktygsfältet. 

        ![Länkad Azure SQL Database-tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Skapa en dummy datamängd för utdata
Denna datamängd för utdata är en dummy datamängd som schemat för pipeline-enheter. Observera att frekvensen är inställd på timme och intervall har angetts till 1. Därför körs pipelinen när en timme i pipeline- och sluttider. 

1. I den vänstra rutan på den Data Factory-redigeraren, klickar du på **... Flera** -> **ny datamängd** -> **Azure SQL**.

    ![Flera -> ny datauppsättning](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Kopiera följande JSON-fragment till JSON-redigerare i den högra rutan. 
    
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
3. Klicka på **Distribuera** i verktygsfältet. Den här åtgärden distribuerar datauppsättningen till Azure Data Factory-tjänsten. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med aktiviteten lagrad procedur 
I det här steget skapar du en pipeline med en lagrad procedur-aktivitet. Aktiviteten anropar sp_executesql lagrade proceduren för att köra SSIS-paket. 

1. I den vänstra rutan klickar du på **... More (Mer)** och sedan på **Ny pipeline**.
2. Kopiera följande JSON-fragment till JSON-Redigerare: 

    > [!IMPORTANT]
    > Ersätt &lt;mappnamn&gt;, &lt;projektnamn&gt;, &lt;paketnamnet&gt; med namn på mappen, projekt och paketet i katalogen SSIS innan du sparar filen.

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
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Klicka på **Distribuera** i verktygsfältet. Den här åtgärden distribuerar pipeline till Azure Data Factory-tjänsten. 

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen
Schemat på datamängd för utdata har definierats som en gång i timmen. Sluttid för pipelinen är fem timmar från starttiden. Därför kan se du fem pipelinen körs. 

1. Stäng fönstren redigeraren så att du ser sidan för data factory. Klicka på **övervaka och hantera** panelen. 

    ![Ikonen Diagram](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Uppdatering av **starttid** och **sluttiden** till **2018-01/18 08:30 AM** och **2018-01/20 08:30 AM**, och klicka på **tillämpa**. Du bör se den **aktivitet windows** som är kopplade till pipelinen körs. 

    ![Aktiviteten windows](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Mer information om hur du övervakar pipelines finns [övervaka och hantera Azure Data Factory pipelines med hjälp av övervakning och hantering av App](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa Data Factory-pipelinen med en lagrad procedur-aktivitet som anropar ett SSIS-paket.

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Skapa en datafabrik
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
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst om du vill länka din Azure SQL-databas som är värd för SSIS-katalog till din data factory. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSIDB-databasen och kör en lagrad procedur om du vill köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i **C:\ADF\RunSSISPackage** mappen med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;användarnamn&gt;@&lt;servername&gt; och &lt;lösenord&gt; med värden för din Azure SQL-databas innan Spara filen.

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
2. I **Azure PowerShell**, växla till den **C:\ADF\RunSSISPackage** mapp.
3. Kör den **ny AzureRmDataFactoryLinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
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

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med aktiviteten lagrad procedur 
I det här steget skapar du en pipeline med en lagrad procedur-aktivitet. Aktiviteten anropar sp_executesql lagrade proceduren för att köra SSIS-paket. 

1. Skapa en JSON-fil med namnet **MyPipeline.json** i den **C:\ADF\RunSSISPackage** mappen med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;mappnamn&gt;, &lt;projektnamn&gt;, &lt;paketnamnet&gt; med namn på mappen, projekt och paketet i katalogen SSIS innan du sparar filen.

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

2. Att skapa pipelinen: **RunSSISPackagePipeline**kör den **ny AzureRmDataFactoryPipeline** cmdlet.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

2. Kör **Get-AzureRmDataFactorySlice** att få information om alla segment i den utgående dataset **, vilket är utdatatabellen för pipelinen.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. 
3. Kör **Get-AzureRmDataFactoryRun** för att hämta information om aktiviteten som körs för en viss sektor.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. 

    Du kan köra följande fråga mot SSIDB-databasen i din Azure SQL-server för att kontrollera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om aktiviteten lagrad procedur, finns det [lagrade proceduraktiviteten](data-factory-stored-proc-activity.md) artikel.

