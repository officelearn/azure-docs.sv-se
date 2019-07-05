---
title: Köra SSIS-paket med aktiviteten kör SSIS-paket – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kör ett SQL Server Integration Services (SSIS)-paket i Azure Data Factory-pipeline med aktiviteten kör SSIS-paket.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484840"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Kör ett SSIS-paket med aktiviteten kör SSIS-paket i Azure Data Factory
Den här artikeln beskriver hur du kör ett SQL Server Integration Services (SSIS)-paket i Azure Data Factory (ADF) pipeline med aktiviteten kör SSIS-paket. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skapa en Azure-SSIS Integration Runtime (IR) om du inte har redan genom att följa de stegvisa anvisningarna i den [självstudien: Etablera Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Kör ett paket i Azure portal
I det här avsnittet ska du använda ADF User Interface (UI) / appen att skapa en ADM pipeline med köra SSIS-paket-aktivitet som kör SSIS-paket.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en aktivitet kör SSIS-paket
I det här steget använder du ADF användargränssnitt/app för att skapa en pipeline. Du lägger till en köra SSIS-paket-aktivitet i pipelinen och konfigurera den för att köra dina SSIS-paket. 

1. I ADF översikt/startsidan i Azure-portalen, klickar du på den **författare och Övervakare** att starta Användargränssnittet för ADF/app på en separat flik. 

   ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   På den **nu sätter vi igång** klickar du på **skapa pipeline**: 

   ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. I den **aktiviteter** verktygslådan Expandera **Allmänt**, dra och släpp en **köra SSIS-paket** aktiviteten till pipelinedesignytan. 

   ![Drar du köra SSIS-paket till designytan](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. På den **Allmänt** fliken för aktiviteten kör SSIS-paket, ange ett namn och beskrivning för aktiviteten. Ange tidsgränsen för valfritt och försök värden.

   ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. På den **inställningar** för aktiviteten kör SSIS-paket, Välj en Azure-SSIS IR där du vill köra ditt paket. Om ditt paket använder Windows-autentisering för att få åtkomst till datalager, t.ex. SQL-servrar/filresurser lokalt, Azure Files osv., kontrollera den **Windows-autentisering** kryssrutan och ange värden för dina autentiseringsuppgifter för körning av paketet (**Domän**/**användarnamn**/**lösenord**). Du kan också använda hemligheter som lagras i din Azure Key Vault (AKV) som deras värden. Om du vill göra det klickar du på den **AZURE KEY VAULT** kryssrutan bredvid de relevanta autentiseringsuppgifterna väljer/Redigera din befintliga AKV länkad tjänst eller skapa en ny och välj sedan den hemliga namn/versionen för din autentiseringsuppgiftsvärde.  När du skapar eller redigerar AKV länkade tjänsten, kan du markera och redigera dina befintliga AKV eller skapa en ny, men ge ADF hanterad identitet åtkomst till din AKV om du inte har gjort det redan. Du kan också ange dina hemligheter direkt i följande format: `<AKV linked service name>/<secret name>/<secret version>`. Om ditt paket måste 32-bitars runtime för att köra, kontrollera den **32-bitars runtime** kryssrutan. 

   För **paketera plats**väljer **SSISDB**, **File System (paket)** , eller **File System (projekt)** . Om du väljer **SSISDB** som din plats för paketet, som väljs automatiskt om din Azure-SSIS IR etablerades med SSIS-katalogen (SSISDB) som värd för Azure SQL Database-server/hanterad instans, måste du ange ditt paket Om du vill köra som har distribuerats till SSISDB. Om din Azure-SSIS IR körs och **manuella transaktioner** kryssrutan är avmarkerad kan du bläddra och välj din befintliga mappar/projekt /-paket/miljöer från SSISDB. Klicka på den **uppdatera** knappen för att hämta dina nyligen tillagda mappar/projekt /-paket/miljöer från SSISDB, så att de är tillgängliga för bläddring och val av. 
   
   För **loggningsnivån**, väljer du en fördefinierad omfattning av loggning för körning av paket. Kontrollera den **anpassad** markerar du kryssrutan om du vill ange namnet på din anpassade loggning i stället. 

   ![Ange egenskaper på fliken Inställningar - automatisk](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Om din Azure-SSIS IR inte körs eller **manuella transaktioner** är markerad, du kan ange sökvägar för paket- och miljö från SSISDB direkt i följande format: `<folder name>/<project name>/<package name>.dtsx` och `<folder name>/<environment name>`.

   ![Ange egenskaper på fliken inställningar - manuellt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Om du väljer **File System (paket)** som din plats för paketet, som väljs automatiskt om din Azure-SSIS IR etablerades utan SSISDB, måste du ange ditt paket ska köras genom att tillhandahålla en Universal Naming Convention ( UNC-sökväg) till din paketfil (`.dtsx`) i den **paketsökväg**. Till exempel, om du sparar dina paket i Azure Files, dess paketsökväg debiteras `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Om du konfigurerar ditt paket i en separat fil kan du också behöva ange en UNCsökväg till konfigurationsfilen (`.dtsConfig`) i den **konfigurationssökväg**. Till exempel om du sparar din konfiguration i Azure Files, dess konfigurationssökväg kommer att `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Ange egenskaper på fliken inställningar - manuellt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Om du väljer **File System (projekt)** som din plats för paketet, måste du ange ditt paket ska köras genom att ange en UNCsökväg till projektfilen (`.ispac`) i den **Projektsökväg** och ett paket () `.dtsx`) från ditt projekt i den **paketnamn**. Till exempel om du sparar ditt projekt i Azure Files, dess Projektsökväg kommer att `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Ange egenskaper på fliken inställningar - manuellt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Därefter måste du ange autentiseringsuppgifter för att komma åt dina projekt/paket/konfiguration-filer. Om du har tidigare angett värdena för dina autentiseringsuppgifter för körning paketet (se ovan) kan du återanvända dem genom att markera den **densamma som paketet körning autentiseringsuppgifter** kryssrutan. I annat fall måste du ange värdena för dina autentiseringsuppgifter för åtkomst av paketet (**domän**/**användarnamn**/**lösenord**). Om du lagrar dina projekt/paket/konfiguration i Azure Files, till exempel den **domän** är `Azure`; **användarnamn** är `<storage account name>`; och **lösenord**är `<storage account key>`. Du kan också använda hemligheter som lagras i din AKV som deras värden (se ovan). Dessa autentiseringsuppgifter används för att komma åt paket- och underordnade paketen i köra paketet uppgiften från sina egna sökväg/samma projekt, samt inställningar, till exempel de som anges i dina paket. 
   
   Om du har använt den **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** protection nivå när du skapar ditt paket via SQL Server Data Tools (SSDT), måste du ange värdet lösenordet i den **Krypteringslösenord**. Du kan också använda en hemlighet som lagras i din AKV som sitt värde (se ovan). Om du har använt den **EncryptSensitiveWithUserKey** skyddsnivån, du måste ange på nytt dina känsliga värden i konfigurationsfiler eller på den **SSIS parametrar** /  **Anslutningen chefer**/**egenskapen åsidosätter** flikarna (se nedan). Om du har använt den **EncryptAllWithUserKey** skyddsnivån, är det som inte stöds, så du behöver konfigurera om ditt paket om du vill använda andra skyddsnivån via SSDT eller `dtutil` kommandoradsverktyget. 
   
   För **loggningsnivån**, väljer du en fördefinierad omfattning av loggning för körning av paket. Kontrollera den **anpassad** markerar du kryssrutan om du vill ange namnet på din anpassade loggning i stället. Om du vill logga dina paket körningar än genom att använda standardloggen-leverantörer som kan anges i ditt paket måste du ange din loggmappen genom att ange dess UNC-sökväg i den **Loggningssökvägen**. Till exempel om du lagrar dina loggar i Azure Files kan din Loggningssökvägen blir `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. En undermapp skapas i den här sökvägen för varje enskild paketet körning och med namnet på aktiviteten kör SSIS-paket körnings-ID, som loggfiler ska skapas var femte minut. 
   
   Slutligen kan behöva du även ange autentiseringsuppgifter för att komma åt din loggmappen. Om du har tidigare angett värdena för dina autentiseringsuppgifter för paketet (se ovan) kan du återanvända dem genom att kontrollera den **densamma som paketet autentiseringsuppgifter** kryssrutan. I annat fall måste du ange värdena för dina autentiseringsuppgifter för åtkomst av loggning (**domän**/**användarnamn**/**lösenord**). Om du lagrar dina loggar i Azure Files, till exempel den **domän** är `Azure`; **användarnamn** är `<storage account name>`; och **lösenord** är `<storage account key>`. Du kan också använda hemligheter som lagras i din AKV som deras värden (se ovan). Dessa autentiseringsuppgifter används för att lagra dina loggar. 
   
   För alla UNC-sökvägar som nämns ovan, fullständigt kvalificerade filnamnet måste vara kortare än 260 tecken och katalognamnet måste innehålla färre än 248 tecken.

5. På den **SSIS parametrar** flik för att köra SSIS-paket aktivitet, om din Azure-SSIS IR körs **SSISDB** är markerad som plats för paketet och **manuella transaktioner** kryssrutan på **inställningar** fliken är avmarkerat, befintliga SSIS-parametrar i ditt valda projekt /-paket från SSISDB visas som du kan tilldela dem värden. Annars kan ange du dem genom en för att tilldela dem värden manuellt – kontrollera att de finns och har angetts korrekt för körning av paket ska lyckas. 
   
   Om du har använt den **EncryptSensitiveWithUserKey** skyddsnivån när du skapar ditt paket via SSDT och **File System (paket)** /**File System (projekt)** är markerad som plats för paketet du också behöva ange dina känsliga parametrar för att tilldela dem värden i konfigurationsfiler eller på den här fliken på nytt. 
   
   När du tilldelar värden till parametrarna, kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, ADF systemvariabler och ADF pipeline parametrar/variablerna. Du kan också använda hemligheter som lagras i din AKV som deras värden (se ovan).

   ![Ange egenskaper på fliken SSIS-parametrar](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. På den **anslutning chefer** flik för att köra SSIS-paket aktivitet, om din Azure-SSIS IR körs **SSISDB** är markerad som plats för paketet och **manuella transaktioner**kryssrutan på **inställningar** fliken är avmarkerat, den befintliga anslutning hanterare i ditt valda projekt /-paket från SSISDB visas för att tilldela värden till deras egenskaper. Annars kan ange du dem genom en för att tilldela värden till deras egenskaper manuellt – kontrollera att de finns och har angetts korrekt för körning av paket ska lyckas. 
   
   Om du har använt den **EncryptSensitiveWithUserKey** skyddsnivån när du skapar ditt paket via SSDT och **File System (paket)** /**File System (projekt)** är markerad som plats för paketet du också behöva ange dina känsliga anslutningsegenskaper manager för att tilldela dem värden i konfigurationsfiler eller på den här fliken på nytt. 
   
   När du tilldelar värden till din manager anslutningsegenskaper, du kan lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, ADF systemvariabler och ADF pipeline parametrar/variablerna. Du kan också använda hemligheter som lagras i din AKV som deras värden (se ovan).

   ![Ange egenskaper på fliken anslutning chefer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. På den **egenskapen åsidosätter** fliken för aktiviteten kör SSIS-paket, kan du ange sökvägar för befintliga egenskaper i ditt valda paket en i taget för att tilldela dem värden manuellt – kontrollera att de finns och är korrekt har angetts för körning av paket ska lyckas, t.ex. Om du vill åsidosätta värdet för användarvariabeln att ange dess sökväg i formatet: `\Package.Variables[User::<variable name>].Value`. 
   
   Om du har använt den **EncryptSensitiveWithUserKey** skyddsnivån när du skapar ditt paket via SSDT och **File System (paket)** /**File System (projekt)** är markerad som plats för paketet du också behöva ange dina känsliga egenskaper för att tilldela dem värden i konfigurationsfiler eller på den här fliken på nytt. 
   
   När du tilldelar värden till dina egenskaper, kan du lägga till dynamiskt innehåll genom att använda uttryck, funktioner, ADF systemvariabler och ADF pipeline parametrar/variablerna.

   ![Ange egenskaper på fliken egenskapen åsidosätter](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   De värden som angetts i konfigurationsfiler och på den *SSIS parametrar* fliken kan åsidosättas med hjälp av den **anslutning chefer**/**egenskapen åsidosätter** flikar, medan de tilldelas den **anslutning chefer** fliken också kan åsidosättas med hjälp av den **egenskapen åsidosätter** fliken.

8. Verifiera pipeline-konfigurationen genom att klicka på **verifiera** i verktygsfältet. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>** .

9. Publicera pipelinen till ADF genom att klicka på **publicera alla** knappen. 

### <a name="run-the-pipeline"></a>Köra en pipeline
I det här steget ska utlösa du en pipelinekörning. 

1. För att utlösa en pipelinekörning klickar du på **utlösaren** i verktygsfältet och sedan på **Utlös nu**. 

   ![Utlös nu](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du ser pipelinekörningen och dess status tillsammans med annan information (till exempel kör starttid). Om du vill uppdatera vyn klickar du på **Uppdatera**.

   ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Du ser bara en aktivitetskörning eftersom pipelinen har endast en aktivitet (köra SSIS-paket-aktivitet).

   ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Du kan köra följande **fråga** mot SSISDB-databasen i Azure SQL-servern att kontrollera att paketet körs. 

   ```sql
   select * from catalog.executions
   ```

   ![Verifiera paketet körningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Du kan också hämta SSISDB körnings-ID från utdata från aktiviteten pipelinekörning och använda det ID: T för att kontrollera mer omfattande körningsloggar och felmeddelanden i SQL Server Management Studio (SSMS).

   ![Få körnings-ID.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägga en hel pipeline med en utlösare

Du kan också skapa en schemalagd utlösare för din pipeline, så att pipelinen körs enligt ett schema (varje timme, varje dag, osv.). Ett exempel finns i [och skapa en data factory - Användargränssnittet för Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Kör ett paket med PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en ADF-pipeline med köra SSIS-paket-aktivitet som kör SSIS-paket. 

Installera de senaste Azure PowerShell-modulerna genom att följa de stegvisa anvisningarna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Skapa en ADM med Azure-SSIS IR
Du kan använda en befintlig ADM som redan har Azure-SSIS IR som etablerats eller skapa en ny ADF med Azure-SSIS IR följa de stegvisa anvisningarna i den [självstudien: Distribuera SSIS-paket till Azure via PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en aktivitet kör SSIS-paket 
I det här steget skapar du en pipeline med en aktivitet kör SSIS-paket. Aktiviteten kör SSIS-paket. 

1. Skapa en JSON-fil med namnet **RunSSISPackagePipeline.json** i den **C:\ADF\RunSSISPackage** mapp med innehåll som liknar följande exempel:

   > [!IMPORTANT]
   > Ersätt objektnamn, beskrivningar och sökvägar, rättigheter och parametervärden, lösenord och andra variabelvärden innan du sparar filen. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. I Azure PowerShell växlar du till den `C:\ADF\RunSSISPackage` mapp.

3. Att skapa pipelinen **RunSSISPackagePipeline**, kör den **Set-AzDataFactoryV2Pipeline** cmdlet.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Här är exempel på utdata:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Köra en pipeline
Använd den **Invoke-AzDataFactoryV2Pipeline** cmdlet för att köra en pipeline. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

Du kan också övervaka pipelinen med hjälp av Azure portal. Stegvisa instruktioner finns i [övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägga en hel pipeline med en utlösare
I föregående steg körde du den pipeline på begäran. Du kan också skapa en schemautlösare för att köra pipelinen enligt ett schema (varje timme, varje dag, osv.).

1. Skapa en JSON-fil med namnet **MyTrigger.json** i **C:\ADF\RunSSISPackage** mapp med följande innehåll: 

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
           }]
       }
   }    
   ```
2. I **Azure PowerShell**, växla till den **C:\ADF\RunSSISPackage** mapp.
3. Kör den **Set-AzDataFactoryV2Trigger** cmdlet, vilket skapar utlösaren. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Som standard har utlösaren stoppats. Starta utlösaren genom att köra den **Start AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Bekräfta att utlösaren har startats genom att köra den **Get-AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Kör följande kommando efter nästa timma. Till exempel om den aktuella tiden är 15:25:00 UTC, kör kommandot på 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Du kan köra följande fråga mot SSIDB-databasen i Azure SQL-servern att kontrollera att paketet körs. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Nästa steg
Se i följande blogginlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
