---
title: Kör SSIS-paket med aktiviteten kör SSIS-paket – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kör ett SQL Server Integration Services-paket (SSIS) i Azure Data Factory pipeline med hjälp av aktiviteten kör SSIS-paket.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000647"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Kör ett SSIS-paket med aktiviteten kör SSIS-paket i Azure Data Factory
Den här artikeln beskriver hur du kör ett SQL Server Integration Services-paket (SSIS) i Azure Data Factory (ADF)-pipeline med hjälp av aktiviteten kör SSIS-paket. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skapa en Azure-SSIS integration Runtime (IR) om du inte redan har en genom att följa de stegvisa anvisningarna i [självstudien: Etablering Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Kör ett paket i Azure Portal
I det här avsnittet ska du använda ADF-/app (User Interface) för att skapa en ADF-pipeline med kör SSIS-paket-aktivitet som kör ditt SSIS-paket.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en EXECUTE SSIS-paket-aktivitet
I det här steget ska du använda ADF UI/app för att skapa en pipeline. Du lägger till en aktivitet för att köra SSIS-paket i pipelinen och konfigurerar den att köra ditt SSIS-paket. 

1. På sidan ADF-översikt/start sida i Azure Portal klickar du på panelen **författare & Monitor** för att starta ADF-gränssnittet/-appen på en separat flik. 

   ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   På sidan **nu sätter vi igång** klickar du på **skapa pipeline**: 

   ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. I verktygs lådan **aktiviteter** expanderar du **Allmänt**och drar sedan & släppa en aktivitet för att **köra SSIS-paket** på pipelinens design yta. 

   ![Dra en aktivitet för att köra SSIS-paket till design ytan](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Ange ett namn och en beskrivning för aktiviteten på fliken **Allmänt** för AKTIVITETEN kör SSIS-paket. Ange valfria värden för timeout och försök.

   ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. På fliken **Inställningar** för AKTIVITETEN kör SSIS-paket väljer du en Azure-SSIS IR där du vill köra paketet. Om ditt paket använder Windows-autentisering för att komma åt data lager, t. ex. SQL-servrar/fil resurser lokalt, Azure Files osv., markerar du kryss rutan **Windows-autentisering** och anger värdena för dina paket körnings uppgifter (**domän** Lösen ord för **användar namn**/). / Du kan också använda hemligheter som lagras i din Azure Key Vault (AKV) som deras värden. Det gör du genom att klicka på kryss rutan **Azure Key Vault** bredvid relevanta autentiseringsuppgifter, välja/Redigera din befintliga AKV-länkade tjänst eller skapa en ny och sedan välja det hemliga namnet/versionen för ditt Credential-värde.  När du skapar/redigerar din AKV-länkade tjänst kan du välja/Redigera din befintliga AKV eller skapa en ny, men ge ADF hanterad identitets åtkomst till din AKV om du inte redan har gjort det. Du kan också ange dina hemligheter direkt i följande format: `<AKV linked service name>/<secret name>/<secret version>`. Om ditt paket kräver att 32-bitars körning körs kontrollerar du kryss rutan **32-bit runtime** . 

   För **paket plats**väljer du **SSISDB**, **fil system (paket)** eller **fil system (projekt)** . Om du väljer **SSISDB** som paket plats, som väljs automatiskt om din Azure-SSIS IR etablerades med SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans, måste du ange ditt paket som ska köras distribuerad till SSISDB. Om din Azure-SSIS IR körs och kryss rutan **manuella poster** är omarkerad kan du bläddra och välja dina befintliga mappar/projekt/paket/miljöer från SSISDB. Klicka på knappen **Uppdatera** för att hämta nyligen tillagda mappar/projekt/paket/miljöer från SSISDB, så att de är tillgängliga för bläddring och val. Om du vill bläddra i eller välja miljöer för dina paket körningar måste du konfigurera dina projekt i förväg för att lägga till dessa miljöer som referenser från samma mappar under SSISDB. Mer information finns i [skapa/MAPPA SSIS miljöer](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   För **loggnings nivå**väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. 

   ![Ange egenskaper på fliken Inställningar-automatisk](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Om Azure-SSIS IR inte körs eller om kryss rutan **manuella poster** är markerad kan du ange paket-och miljö Sök vägar från SSISDB direkt i följande format: `<folder name>/<project name>/<package name>.dtsx` och. `<folder name>/<environment name>`

   ![Ange egenskaper på fliken Inställningar – manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Om du väljer **fil system (paket)** som paket plats, som väljs automatiskt om Azure-SSIS IR etablerades utan SSISDB, måste du ange att paketet ska köras genom att ange en Universal Naming Convention UNC-sökväg till din paket fil (`.dtsx`) i **paket Sök vägen**. Om du till exempel lagrar ditt paket i Azure Files, är dess paket Sök väg `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Om du konfigurerar ditt paket i en separat fil måste du också ange en UNC-sökväg till konfigurations filen (`.dtsConfig`) i **konfigurations Sök vägen**. Om du till exempel lagrar konfigurationen i Azure Files, är dess konfigurations Sök väg `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Ange egenskaper på fliken Inställningar – manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Om du väljer **fil system (projekt)** som paket plats måste du ange att paketet ska köras genom att tillhandahålla en UNC-sökväg till projekt filen (`.ispac`) i **projekt Sök vägen** och en paketfil (`.dtsx`) från projektet i  **Paket namn**. Om du till exempel lagrar projektet i Azure Files, är dess projekt Sök väg `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Ange egenskaper på fliken Inställningar – manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Sedan måste du ange autentiseringsuppgifterna för att komma åt dina projekt/paket/konfigurationsfiler. Om du tidigare har angett värdena för paketets körnings uppgifter (se ovan) kan du återanvända dem genom att markera kryss rutan **samma som för paket körnings behörighet** . Annars måste du ange värdena för dina paket åtkomst uppgifter (**domän**/**användar namn**/**lösen ord**). Om du till exempel lagrar ditt projekt/paket/konfiguration i Azure Files, är `Azure` **domänen** `<storage account name>`, **användar namnet** och **lösen ordet** `<storage account key>`. Du kan också använda hemligheter som lagrats i din AKV som deras värden (se ovan). Dessa autentiseringsuppgifter används för att komma åt ditt paket och underordnade paket i åtgärden kör paket, allt från sin egen sökväg/samma projekt, samt konfigurationer, inklusive de som anges i dina paket. 
   
   Om du har använt skydds nivån **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** när du skapar paketet via SQL Server Data Tools (SSDT) måste du ange värdet för lösen ordet i **krypteringen lösen ord**. Du kan också använda en hemlighet som lagrats i din AKV som dess värde (se ovan). Om du har använt skydds nivån **EncryptSensitiveWithUserKey** måste du ange dina känsliga värden i konfigurationsfiler på nytt eller på **SSIS parametrar**/-egenskapen**anslutnings hanterare**/ **Åsidosätter** flikar (se nedan). Om du har använt skydds nivån **EncryptAllWithUserKey** stöds den inte, så du måste konfigurera om paketet så att det använder andra skydds nivåer via SSDT eller `dtutil` kommando rads verktyget. 
   
   För **loggnings nivå**väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. Om du vill logga dina paket körningar utöver att använda de standard logg leverantörer som kan anges i ditt paket måste du ange din loggfil genom att ange dess UNC-sökväg i **loggnings Sök vägen**. Om du t `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. ex. lagrar dina loggar i Azure Files, är din loggnings Sök väg. En undermapp skapas i den här sökvägen för varje enskilt paket som har körts och som har namngetts efter körnings-ID: t för aktiviteten kör SSIS-paket, där loggfiler genereras var femte minut. 
   
   Slutligen måste du också ange autentiseringsuppgifterna för att komma åt loggmappen. Om du tidigare har angett värdena för dina paket åtkomst uppgifter (se ovan) kan du återanvända dem genom att markera kryss rutan **samma som paket åtkomst autentiseringsuppgifter** . Annars måste du ange värden för inloggnings uppgifterna för inloggning (**domän**/**användar namn**/**lösen ord**). Om du till exempel lagrar loggarna i Azure Files är `Azure` **domänen** `<storage account name>`, **användar namnet** och **lösen ordet** `<storage account key>`. Du kan också använda hemligheter som lagrats i din AKV som deras värden (se ovan). Dessa autentiseringsuppgifter kommer att användas för att lagra loggarna. 
   
   För alla UNC-sökvägar som nämns ovan måste det fullständigt kvalificerade fil namnet innehålla färre än 260 tecken och katalog namnet måste innehålla färre än 248 tecken.

5. På fliken **SSIS-parametrar** för AKTIVITETEN kör SSIS-paket, om din Azure-SSIS IR körs, väljs **SSISDB** som din paket plats och kryss rutan **manuella poster** på fliken **Inställningar** är omarkerad, befintliga SSIS parametrarna i det valda projektet/paketet från SSISDB kommer att visas så att du kan tilldela dem värden. Annars kan du ange dem en i taget för att tilldela dem värden manuellt – kontrol lera att de finns och att de har angetts korrekt för att paket körningen ska lyckas. 
   
   Om du har använt skydds nivån **EncryptSensitiveWithUserKey** när du skapar paketet via SSDT och **fil**/system (**projekt)** har marker ATS som paket plats, måste du också ange den igen känsliga parametrar för att tilldela värden till dem i konfigurationsfiler eller på den här fliken. 
   
   När du tilldelar värden till parametrarna kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, ADF-systemvariabler och parametrar/variabler för ADF-pipeline. Du kan också använda hemligheter som lagrats i din AKV som deras värden (se ovan).

   ![Ange egenskaper på fliken SSIS-parametrar](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. På fliken **anslutnings hanterare** för AKTIVITETEN kör SSIS paket, om din Azure-SSIS IR körs, väljs **SSISDB** som paket plats och kryss rutan **manuella poster** på fliken **Inställningar** är omarkerad, den befintliga anslutnings hanterare i det valda projektet/paketet från SSISDB kommer att visas så att du kan tilldela värden till deras egenskaper. Annars kan du ange dem en i taget för att tilldela värdena till deras egenskaper manuellt – kontrol lera att de finns och att de har angetts korrekt för att paket körningen ska lyckas. 
   
   Om du har använt skydds nivån **EncryptSensitiveWithUserKey** när du skapar paketet via SSDT och **fil**/system (**projekt)** har marker ATS som paket plats, måste du också ange den igen känsliga egenskaper för anslutnings hanteraren för att tilldela värden till dem i konfigurationsfiler eller på den här fliken. 
   
   När du tilldelar värden till egenskaperna för anslutnings hanteraren kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, ADF-systemvariabler och ADF-parametrar/variabler. Du kan också använda hemligheter som lagrats i din AKV som deras värden (se ovan).

   ![Ange egenskaper på fliken anslutnings hanterare](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. På fliken **åsidosättningar för egenskaper** för att köra SSIS paket-aktivitet kan du ange Sök vägarna för de befintliga egenskaperna i det valda paketet ett i taget för att tilldela dem värden manuellt – kontrol lera att de finns och att de har angetts korrekt för ditt paket körningen ska lyckas, t. ex. om du vill åsidosätta värdet för användar variabeln anger du dess sökväg `\Package.Variables[User::<variable name>].Value`i följande format:. 
   
   Om du har använt skydds nivån **EncryptSensitiveWithUserKey** när du skapar paketet via SSDT och **fil**/system (**projekt)** har marker ATS som paket plats, måste du också ange den igen känsliga egenskaper för att tilldela värden till dem i konfigurationsfiler eller på den här fliken. 
   
   När du tilldelar värden till dina egenskaper kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, ADF-systemvariabler och parametrar/variabler för ADF-pipeline.

   ![Ange egenskaper på fliken åsidosättningar för egenskap](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Värdena som tilldelas i konfigurationsfiler och på fliken *SSIS Parameters* kan åsidosättas med hjälp av**egenskapen** för **anslutnings hanterare**/, medan de som tilldelas på fliken **anslutnings hanterare** kan åsidosätts även med hjälp av fliken **åsidosättningar för egenskaper** .

8. Verifiera pipeline-konfigurationen genom att klicka på **Verifiera** i verktygsfältet. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>** .

9. Publicera pipelinen till ADF genom att klicka på knappen **publicera alla** . 

### <a name="run-the-pipeline"></a>Köra en pipeline
I det här steget utlöser du en pipeline-körning. 

1. Om du vill utlösa en pipeline-körning klickar du på **Utlös** i verktygsfältet och klickar sedan på **Utlös nu**. 

   ![Utlös nu](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du ser pipeline-körningen och dess status tillsammans med annan information (till exempel kör Start tid). Om du vill uppdatera vyn klickar du på **Uppdatera**.

   ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Du ser bara en aktivitets körning eftersom pipelinen bara har en aktivitet (aktiviteten kör SSIS-paket).

   ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Du kan köra följande **fråga** mot SSISDB-databasen i din Azure SQL-Server för att kontrol lera att paketet körs. 

   ```sql
   select * from catalog.executions
   ```

   ![Verifiera paket körningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Du kan också få körnings-ID: t för SSISDB från utdata från pipeline-aktiviteten och använda ID: t för att kontrol lera utförliga körnings loggar och fel meddelanden i SQL Server Management Studio (SSMS).

   ![Hämta körnings-ID: t.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägg pipelinen med en utlösare

Du kan också skapa en schemalagd utlösare för din pipeline så att pipelinen körs enligt ett schema (varje timme, varje dag osv.). Ett exempel finns i [skapa en data fabrik – Data Factory användar gränssnitt](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Köra ett paket med PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en ADF-pipeline med kör SSIS-paket-aktivitet som kör ditt SSIS-paket. 

Installera de senaste Azure PowerShell modulerna genom att följa de stegvisa anvisningarna i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Skapa en ADF med Azure-SSIS IR
Du kan antingen använda en befintlig ADM som redan Azure-SSIS IR etablerad eller skapa en ny ADF med Azure-SSIS IR följa de stegvisa anvisningarna i [självstudien: Distribuera SSIS-paket till Azure via](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)PowerShell.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en EXECUTE SSIS-paket-aktivitet 
I det här steget skapar du en pipeline med en aktivitet för att köra SSIS-paket. Aktiviteten Kör ditt SSIS-paket. 

1. Skapa en JSON-fil med namnet **RunSSISPackagePipeline. JSON** i mappen **C:\ADF\RunSSISPackage** med innehåll som liknar följande exempel:

   > [!IMPORTANT]
   > Ersätt objekt namn/beskrivningar/sökvägar, egenskaper/parameter värden, lösen ord och andra variabel värden innan du sparar filen. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
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
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
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

   Om du vill köra paket som lagras i fil system/fil resurser/Azure Files, kan du ange värdena för egenskaperna för paket/logg plats enligt följande.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Om du vill köra paket i projekt som lagras i fil system/fil resurser/Azure Files, kan du ange värdena för din paket plats egenskap enligt följande.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. I Azure PowerShell växlar du till `C:\ADF\RunSSISPackage` mappen.

3. Om du vill skapa pipelinen **RunSSISPackagePipeline**kör du cmdleten **set-AzDataFactoryV2Pipeline** .

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
Använd cmdleten **Invoke-AzDataFactoryV2Pipeline** för att köra pipelinen. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

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

Du kan också övervaka pipelinen med hjälp av Azure Portal. Stegvisa instruktioner finns i [övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägg pipelinen med en utlösare
I föregående steg körde du pipelinen på begäran. Du kan också skapa en schema utlösare för att köra pipelinen enligt ett schema (varje timme, varje dag osv.).

1. Skapa en JSON-fil med namnet **untrigger. JSON** i mappen **C:\ADF\RunSSISPackage** med följande innehåll: 

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
2. I **Azure PowerShell**växlar du till mappen **C:\ADF\RunSSISPackage**
3. Kör cmdleten **set-AzDataFactoryV2Trigger** som skapar utlösaren. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Som standard är utlösaren i stoppat läge. Starta utlösaren genom att köra cmdleten **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Bekräfta att utlösaren har startats genom att köra cmdleten **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Kör följande kommando efter nästa timma. Om den aktuella tiden till exempel är 3:25 PM, kör du kommandot på 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Du kan köra följande fråga mot SSISDB-databasen i din Azure SQL-Server för att kontrol lera att paketet körs. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Nästa steg
Se följande blogg inlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF-pipeline](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
