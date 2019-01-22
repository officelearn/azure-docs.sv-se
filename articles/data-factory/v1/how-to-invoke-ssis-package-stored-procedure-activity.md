---
title: Anropa SSIS-paket med Azure Data Factory - lagrade Proceduraktiviteten | Microsoft Docs
description: Den här artikeln beskriver hur du anropar ett SQL Server Integration Services (SSIS)-paket från en Azure Data Factory-pipeline med hjälp av den lagrade Proceduraktiviteten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: c7731de810dab8b252294d694ace5df3f5d0a185
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427567"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Anropa ett SSIS-paket med hjälp av aktivitet för lagrad procedur i Azure Data Factory
Den här artikeln beskriver hur du anropar ett SSIS-paket från en Azure Data Factory-pipeline med hjälp av en lagrad procedur-aktivitet. 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [anropa SSIS-paket med lagrad proceduraktivitet i](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-sql-database"></a>Azure SQL Database 
I den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en Azure SQL Database Managed Instance.

### <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS integration runtime om du inte har en genom att följa de stegvisa anvisningarna i den [självstudien: Distribuera SSIS-paket](../tutorial-create-azure-ssis-runtime-portal.md). Du kan inte använda Data Factory version 1 för att skapa en Azure-SSIS integration runtime. 

## <a name="azure-portal"></a>Azure Portal
I det här avsnittet använder du Azure-portalen för att skapa en Data Factory-pipeline med en lagrad procedur-aktivitet som anropar ett SSIS-paket.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Första steget är att skapa en datafabrik med hjälp av Azure portal. 

1. Navigera till [Azure-portalen](https://portal.azure.com). 
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.

    `Data factory name ADFTutorialDataFactory is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
    Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../../azure-resource-manager/resource-group-overview.md).  
4. Välj **V1** för den **version**.
5. Välj **plats** för datafabriken. Endast platser som stöds av Data Factory visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas på andra platser.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klicka på **författare och distribuera** att starta Data Factory-redigeraren.

    ![Data Factory-redigeraren](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst för att länka Azure SQL database som är värd för SSIS-katalogen till din datafabrik. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSISDB-databasen och kör en lagrad procedur om du vill köra ett SSIS-paket. 

1. I Data Factory-redigeraren, klicka på **Nytt datalager** till menyn och klicka på **Azure SQL Database**. 

    ![Nytt datalager -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. I den högra rutan, gör du följande steg:

    1. Ersätt `<servername>` med namnet på Azure SQL-servern. 
    2. Ersätt `<databasename>` med **SSISDB** (namn på SSIS-katalogdatabasen). 
    3. Ersätt `<username@servername>` med namnet på den användare som har åtkomst till Azure SQL-servern. 
    4. Ersätt `<password>` med lösenordet för användaren. 
    5. Distribuera den länkade tjänsten genom att klicka på den **distribuera** i verktygsfältet. 

        ![Länkad Azure SQL Database-tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Skapa en dummy datauppsättning för utdata
Det här är en dummy datauppsättning som styr schemat för pipelinen. Lägg märke till att frekvensen är inställd på Hour och interval anges till 1. Därför körs pipelinen när en timme i pipeline- och sluttider. 

1. I den vänstra rutan i Data Factory-redigeraren klickar du på **... Mer** -> **ny datauppsättning** -> **Azure SQL**.

    ![Mer -> ny datauppsättning](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Kopiera följande JSON-kodfragment i JSON-redigeraren i den högra rutan. 
    
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

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med en aktivitet för lagrad procedur 
I det här steget skapar du en pipeline med en lagrad procedur-aktivitet. Aktiviteten anropar sp_executesql lagrade proceduren för att köra dina SSIS-paket. 

1. I den vänstra rutan klickar du på **... More (Mer)** och sedan på **Ny pipeline**.
2. Kopiera följande JSON-kodfragment i JSON-redigeraren: 

    > [!IMPORTANT]
    > Ersätt &lt;mappnamn&gt;, &lt;projektnamn&gt;, &lt;paketnamn&gt; med namn på mapp, projekt och paket i SSIS-katalogen innan du sparar filen.

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
3. Klicka på **Distribuera** i verktygsfältet. Den här åtgärden distribuerar pipelinen till Azure Data Factory-tjänsten. 

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen
Schemat i datauppsättningen för utdata har definierats som per timme. Sluttid för pipelinen är fem timmar från starttiden. Därför kan se du fem pipelinekörningar. 

1. Stäng fönstren redigeraren så att du kan se startsidan för datafabriken. Klicka på **övervaka och hantera** panelen. 

    ![Ikonen Diagram](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Uppdatera den **starttid** och **sluttid** till **01/18/2018 kl. 08:30** och **2018-01-20 kl. 08:30**, och klicka på **tillämpa**. Du bör se den **aktivitetsfönster** som är associerade med pipelinekörningen. 

    ![Aktivitetsfönster](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Mer information om hur du övervakar pipelines finns i [övervaka och hantera Azure Data Factory-pipelines med övervaknings- och Hanteringsappen](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en lagrad procedur-aktivitet som anropar ett SSIS-paket.

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Följande procedur innehåller steg för att skapa en datafabrik. Du kan skapa en pipeline med en aktivitet för lagrad procedur i den här datafabriken. Lagrad procedur-aktivitet kör en lagrad procedur i SSISDB-databasen för att köra dina SSIS-paket.

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

5. Om du vill skapa data factory, kör du följande **New-AzureRmDataFactory** cmdlet, med hjälp av den platsen och egenskapen ResourceGroupName från variabeln $ResGrp: 
    
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
Skapa en länkad tjänst för att länka Azure SQL database som är värd för SSIS-katalogen till din datafabrik. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSISDB-databasen och kör en lagrad procedur om du vill köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i **C:\ADF\RunSSISPackage** mapp med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;användarnamn&gt;@&lt;servername&gt; och &lt;lösenord&gt; med värdena för din Azure SQL Database innan sparar filen.

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
3. Kör cmdleten **New-AzureRmDataFactoryLinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Det här är en dummy datauppsättning som styr schemat för pipelinen. Lägg märke till att frekvensen är inställd på Hour och interval anges till 1. Därför körs pipelinen när en timme i pipeline- och sluttider. 

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
2. Kör den **New AzureRmDataFactoryDataset** cmdlet för att skapa en datauppsättning. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med en aktivitet för lagrad procedur 
I det här steget skapar du en pipeline med en lagrad procedur-aktivitet. Aktiviteten anropar sp_executesql lagrade proceduren för att köra dina SSIS-paket. 

1. Skapa en JSON-fil med namnet **MyPipeline.json** i den **C:\ADF\RunSSISPackage** mapp med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;mappnamn&gt;, &lt;projektnamn&gt;, &lt;paketnamn&gt; med namn på mapp, projekt och paket i SSIS-katalogen innan du sparar filen.

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

2. Så här skapar du pipelinen: **RunSSISPackagePipeline**, kör den **New AzureRmDataFactoryPipeline** cmdlet.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

2. Kör **Get-AzureRmDataFactorySlice** att få information om alla sektorer av den utdata datauppsättning **, vilket är utdatatabellen för pipelinen.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. 
3. Kör **Get-AzureRmDataFactoryRun** för att hämta information om aktiviteten som körs för en viss sektor.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. 

    Du kan köra följande fråga mot SSIDB-databasen i Azure SQL-servern att kontrollera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om aktivitet för lagrad procedur, finns det [lagringsprocedur-aktivitet](data-factory-stored-proc-activity.md) artikeln.

