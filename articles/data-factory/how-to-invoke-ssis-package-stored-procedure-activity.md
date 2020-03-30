---
title: Kör SSIS-paket med lagrad proceduraktivitet - Azure
description: I den här artikeln beskrivs hur du kör ett SSIS-paket (SQL Server Integration Services) i en Azure Data Factory-pipeline med hjälp av den lagrade proceduraktiviteten.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 063728c03c689c2eafec889bdee8276772ae685a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444034"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Köra ett SSIS-paket med aktiviteten Lagrad procedur i Azure Data Factory
I den här artikeln beskrivs hur du kör ett SSIS-paket i en Azure Data Factory-pipeline med hjälp av en aktivitet för lagrad procedur. 

## <a name="prerequisites"></a>Krav

### <a name="azure-sql-database"></a>Azure SQL Database 
Genomgången i den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en hanterad Azure SQL-databas-instans.

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS-integreringskörning om du inte har en genom att följa steg-för-steg-instruktionen i [självstudien: Distribuera SSIS-paket](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory UI (Azure-portal)
I det här avsnittet använder du Data Factory UI för att skapa en Data Factory-pipeline med en lagrad proceduraktivitet som anropar ett SSIS-paket.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Första steget är att skapa en datafabrik med hjälp av Azure-portalen. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Navigera till [Azure-portalen](https://portal.azure.com). 
3. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sidan Ny datafabrik](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Namnet på Azure-datafabriken måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Namnet är inte tillgängligt – fel](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
   - Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa ny**och ange namnet på en resursgrupp.   
         
     Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds av Data Factory visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas på andra platser.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med status: **Distribuera datafabrik**. 

     ![panelen distribuerar datafabrik](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
     ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med lagrad proceduraktivitet
I det här steget använder du datafabriksgränssnittet för att skapa en pipeline. Du lägger till en lagrad proceduraktivitet i pipelinen och konfigurerar den för att köra SSIS-paketet med hjälp av den sp_executesql lagrade proceduren. 

1. Klicka på **Skapa pipeline**på sidan Komma igång: 

    ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Expandera aktiviteten **Allmänt**och dra släpp **lagrad procedur** i **verktygslådan Aktiviteter** till pipelinedesignerytan. 

    ![Dra och släpp lagrad proceduraktivitet](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. I egenskapsfönstret för den lagrade proceduraktiviteten växlar du till fliken **SQL-konto** och klickar på **+ Nytt**. Du skapar en anslutning till Azure SQL-databasen som är värd för SSIS-katalogen (SSIDB-databasen). 
   
    ![Knapp för ny länkad tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Välj **Azure SQL Database** för **typ**.
    2. Välj **standardkörning** för Azure-integrering för att ansluta `SSISDB` till Azure SQL Database som är värd för databasen.
    3. Välj den Azure SQL-databas som är värd för SSISDB-databasen för **servernamnfältet.**
    4. Välj **SSISDB** för **databasnamn**.
    5. För **Användarnamn**anger du namnet på den användare som har åtkomst till databasen.
    6. För **Lösenord**anger du lösenordet för användaren. 
    7. Testa anslutningen till databasen genom att klicka på **Knappen Testa anslutning.**
    8. Spara den länkade tjänsten genom att klicka på knappen **Spara.** 

        ![Länkad Azure SQL Database-tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. I egenskapsfönstret växlar du till fliken **Lagrad procedur** på fliken **SQL-konto** och gör följande: 

    1. Välj **Redigera**. 
    2. För fältet **Lagrat procedurnamn** anger du `sp_executesql`. 
    3. Klicka på **+ Nytt** i avsnittet **Lagrade procedurparametrar.** 
    4. Om du **vill ha** parameterns namn anger du **stmt**. 
    5. För **typ** av parameter anger du **Sträng**. 
    6. För **parameterns värde** anger du följande SQL-fråga:

        I SQL-frågan anger du rätt värden för **folder_name,** **project_name**och **package_name** parametrar. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Länkad Azure SQL Database-tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Om du vill validera pipelinekonfigurationen klickar du på **Validera** i verktygsfältet. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

    ![Verifiera pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publicera pipelinen till Data Factory genom att klicka på Knappen **Publicera alla.** 

    ![Publicera](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Kör och övervaka pipelinen
I det här avsnittet utlöser du en pipeline-körning och övervakar den. 

1. Om du vill utlösa en pipelinekörning klickar du på **Utlösare** i verktygsfältet och klickar på **Utlösare nu**. 

    ![Utlös nu](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 
3. Växla till fliken **Övervaka** till vänster. Du ser pipeline-körningen och dess status tillsammans med annan information (till exempel Kör starttid). Om du vill uppdatera vyn klickar du på **Uppdatera**.

    ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Du ser bara en aktivitetskörning eftersom pipelinen bara har en aktivitet (lagrad proceduraktivitet).

    ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Du kan köra följande **fråga** mot SSISDB-databasen i Din Azure SQL-server för att verifiera att paketet har körts. 

    ```sql
    select * from catalog.executions
    ```

    ![Verifiera paketkörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Du kan också skapa en schemalagd utlösare för pipelinen så att pipelinen körs enligt ett schema (timme, dag osv.). Ett exempel finns i [Skapa en datafabrik - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en lagrad proceduraktivitet som anropar ett SSIS-paket. 

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Du kan antingen använda samma datafabrik som har Azure-SSIS IR eller skapa en separat datafabrik. Följande procedur innehåller steg för att skapa en datafabrik. Du skapar en pipeline med en lagrad proceduraktivitet i den här datafabriken. Den lagrade proceduraktiviteten kör en lagrad procedur i SSISDB-databasen för att köra ditt SSIS-paket. 

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
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

5. Om du vill skapa datafabriken kör du följande **Cmdlet Set-AzDataFactoryV2** med egenskapen Location och ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst för att länka din Azure SQL-databas som är värd för SSIS-katalogen till din datafabrik. Data Factory använder information i den här länkade tjänsten för att ansluta till SSISDB-databasen och kör en lagrad procedur för att köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i mappen **C:\ADF\RunSSISPackage** med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt;&gt;servernamn,&gt; &lt;användarnamn &lt;&gt; och lösenord med värden i Din Azure SQL-databas innan du sparar filen.

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

3. Kör cmdleten **Set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med lagrad proceduraktivitet 
I det här steget skapar du en pipeline med en lagrad proceduraktivitet. Aktiviteten anropar den sp_executesql lagrade proceduren för att köra SSIS-paketet. 

1. Skapa en JSON-fil med namnet **RunSSISPackagePipeline.json** i mappen **C:\ADF\RunSSISPackage** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;&gt;MAPPNAMN &lt;,&gt; &lt;PROJEKTNAMN, PAKETNAMN&gt; med namn på mapp, projekt och paket i SSIS-katalogen innan filen sparas. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Så här skapar du pipelinen: **RunSSISPackagePipeline**kör cmdlet **set-azdatafactoryV2Pipeline.**

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Här är exempel på utdata:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning
Använd cmdleten **Invoke-AzDataFactoryV2Pipeline** för att köra pipelinen. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Skapa en utlösare
I föregående steg anropade du pipelinen på begäran. Du kan också skapa en schemautlösare för att köra pipelinen enligt ett schema (varje timme, varje dag osv.).

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen **C:\ADF\RunSSISPackage** med följande innehåll: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. I **Azure PowerShell**växlar du till mappen **C:\ADF\RunSSISPackage.**
3. Kör **cmdleten Set-AzDataFactoryV2Trigger,** vilket skapar utlösaren. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Som standard är utlösaren i stoppat tillstånd. Starta utlösaren genom att köra cmdleten **Start-AzDataFactoryV2Trigger.** 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Bekräfta att utlösaren startas genom att köra cmdleten **Get-AzDataFactoryV2Trigger.** 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Kör följande kommando efter nästa timme. Om den aktuella tiden till exempel är 15:25 UTC kör du kommandot vid 16:00 UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Du kan köra följande fråga mot SSISDB-databasen i Din Azure SQL-server för att verifiera att paketet har körts. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Nästa steg
Du kan också övervaka pipelinen med Azure-portalen. Stegvisa instruktioner finns i [Övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
