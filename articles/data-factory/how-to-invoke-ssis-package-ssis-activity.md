---
title: Kör ett SSIS-paket med aktiviteten Kör SSIS-paket
description: I den här artikeln beskrivs hur du kör ett SSIS-paket (SQL Server Integration Services) i en Azure Data Factory-pipeline med hjälp av aktiviteten Kör SSIS-paket.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: f505313b37d5289a5af10c40ede7f376eab4841d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605963"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Kör ett SSIS-paket med aktiviteten Kör SSIS-paket i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du kör ett SSIS-paket (SQL Server Integration Services) i en Azure Data Factory-pipeline med hjälp av aktiviteten Kör SSIS-paket. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skapa en Azure-SSIS-integreringskörning (IR) om du inte redan har en genom att följa steg-för-steg-instruktionerna i [självstudien: Etablera Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Köra ett paket i Azure-portalen
I det här avsnittet använder du användargränssnittet (Data Factory user interface) eller app för att skapa en Data Factory-pipeline med en kör SSIS-paketaktivitet som kör ditt SSIS-paket.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en körning ssis-paketaktivitet
I det här steget använder du datafabrikens användargränssnitt eller app för att skapa en pipeline. Du lägger till en kör SSIS-paketaktivitet i pipelinen och konfigurerar den för att köra ditt SSIS-paket. 

1. På översikten eller startsidan för Data Factory i Azure-portalen väljer du panelen **Författare & övervakare** för att starta datafabrikens användargränssnitt eller app på en separat flik. 

   ![Startsida för Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. 

   ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Expandera **Allmänt**i **verktygslådan Aktiviteter** . Dra sedan en **kör SSIS-paketaktivitet** till pipelinedesignerytan. 

   ![Dra en körnings-SSIS-paketaktivitet till designerytan](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Ange ett namn och en beskrivning för aktiviteten på fliken **Allmänt** för aktiviteten Kör SSIS-paket. Ange valfria **timeout-** och **återförsöksvärden.**

   ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. På fliken **Inställningar** för aktiviteten Kör SSIS-paket väljer du en Azure-SSIS IR där du vill köra paketet. Om ditt paket använder Windows-autentisering för att komma åt datalager (till exempel SQL-servrar eller filresurser lokalt eller Azure-filer) markerar du kryssrutan **Windows-autentisering.** Ange värdena för autentiseringsuppgifterna för paketkörning i rutorna **Domän,** **Användarnamn**och **Lösenord.** 

    Alternativt kan du använda hemligheter som lagras i ditt Azure-nyckelvalv som deras värden. Det gör du genom att markera kryssrutan **AZURE KEY VAULT** bredvid relevant autentiseringsuppgifter. Markera eller redigera din befintliga nyckelvalvslänkade tjänst eller skapa en ny. Välj sedan det hemliga namnet eller versionen för ditt autentiseringsvärde.

    När du skapar eller redigerar den länkade tjänsten för nyckelvalvet kan du välja eller redigera ditt befintliga nyckelvalv eller skapa ett nytt. Se till att ge Data Factory hanterad identitetsåtkomst till ditt nyckelvalv om du inte redan har gjort det. Du kan också ange dina hemligheter `<Key vault linked service name>/<secret name>/<secret version>`direkt i följande format: . Om ditt paket behöver 32-bitars körning för att köras markerar du kryssrutan **32-bitars körning.**

   För **Paketplats**väljer du **SSISDB**, **File System (Paket),** File System **(Project)** eller **Embedded package**. Om du väljer **SSISDB** som paketplats, som väljs automatiskt om din Azure-SSIS IR har etablerats med SSIS-katalogen (SSISDB) som finns värd för en Azure SQL Database-server eller hanterad instans, anger du ditt paket som ska köras som har distribuerats till SSISDB. 

    Om din Azure-SSIS IR körs och kryssrutan **Manuella poster** är avmarkerad bläddrar du bland och markerar dina befintliga mappar, projekt, paket eller miljöer från SSISDB. Välj **Uppdatera** om du vill hämta de nyligen tillagda mapparna, projekten, paketen eller miljöerna från SSISDB så att de är tillgängliga för surfning och val. Om du vill bläddra i eller välja miljöer för paketkörningar måste du konfigurera dina projekt i förväg för att lägga till dessa miljöer som referenser från samma mappar under SSISDB. Mer information finns i [Skapa och mappa SSIS-miljöer](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   För **loggningsnivå**väljer du ett fördefinierat omfång för loggning för paketkörningen. Markera kryssrutan **Anpassad** om du vill ange ditt anpassade loggningsnamn i stället. 

   ![Ange egenskaper på fliken Inställningar - Automatisk](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Om din Azure-SSIS IR inte körs eller kryssrutan **Manuella poster** är markerad anger du dina paket- och `<folder name>/<project name>/<package name>.dtsx` miljösökvägar från SSISDB direkt i följande format: och `<folder name>/<environment name>`.

   ![Ange egenskaper på fliken Inställningar - Manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Om du väljer **Filsystem (Paket)** som din paketplats, som väljs automatiskt om din Azure-SSIS IR har etablerats utan SSISDB, anger du`.dtsx`ditt paket som ska köras genom att tillhandahålla en UNC-sökväg (Universal Naming Convention) till paketfilen ( ) i rutan **Paketsökväg.** Om du till exempel lagrar paketet i Azure `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`Files är dess paketsökväg . 
   
   Om du konfigurerar paketet i en separat fil måste du också`.dtsConfig`ange en UNC-sökväg till konfigurationsfilen ( ) i rutan **Konfigurationssökväg.** Om du till exempel lagrar konfigurationen i Azure `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`Files är dess konfigurationssökväg .

   ![Ange egenskaper på fliken Inställningar - Manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Om du väljer **Filsystem (Project)** som paketplats anger du paketet som ska`.ispac`köras genom att tillhandahålla en`.dtsx`UNC-sökväg till projektfilen ( ) i rutan **Projektsökväg** och en paketfil ( ) från projektet i rutan **Paketnamn.** Om du till exempel lagrar projektet i Azure `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`Files är dess projektsökväg .

   ![Ange egenskaper på fliken Inställningar - Manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ange sedan autentiseringsuppgifterna för att komma åt projekt-, paket- eller konfigurationsfilerna. Om du tidigare har angett värdena för autentiseringsuppgifterna för paketkörning (se föregående) kan du återanvända dem genom att markera kryssrutan **Samma som autentiseringsuppgifter för paketkörning.** Annars anger du värdena för paketåtkomstautentiseringsuppgifterna i rutorna **Domän,** **Användarnamn**och **Lösenord.** Om du till exempel lagrar ditt projekt, paket eller konfiguration `Azure`i Azure `<storage account name>`Files är `<storage account key>`domänen , användarnamnet är och lösenordet är . 

   Du kan också använda hemligheter som lagrats i nyckelvalvet som värden (se tidigare). Dessa autentiseringsuppgifter används för att komma åt dina paket- och underordnade paket i Kör paketuppgift, allt från sin egen sökväg eller samma projekt, samt konfigurationer, som innehåller de som anges i dina paket. 

   Om du väljer **Inbäddat paket** som paketplats drar du och släpper paketet för att köra eller **ladda upp** det från en filmapp till den medföljande rutan. Ditt paket komprimeras och bäddas in automatiskt i aktivitetsnyttolasten. När du har bäddat in kan du **hämta** paketet senare för redigering. Du kan också **parameterisera** det inbäddade paketet genom att tilldela det till en pipelineparameter som kan användas i flera aktiviteter, och därmed optimera storleken på pipelinenyttolasten. Om ditt inbäddade paket inte är krypterat och vi upptäcker användningen av Kör paketuppgift i det, markeras kryssrutan **Kör paketuppgift** automatiskt och relevanta underordnade paket med sina filsystemreferenser läggs automatiskt till för att du också ska kunna bädda in dem. Om vi inte kan identifiera användningen av Kör paketuppgift måste du manuellt markera kryssrutan **Kör paketuppgift** och lägga till relevanta underordnade paket med sina filsystemreferenser en efter en så att du också kan bädda in dem. Om de underordnade paketen använder SQL Server-referenser kontrollerar du att SQL Server är tillgänglig för din Azure-SSIS IR.  Användningen av projektreferenser för underordnade paket stöds för närvarande inte.
   
   ![Ange egenskaper på fliken Inställningar - Manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Om du använde **encryptallwithpassword** eller **EncryptSensitiveWithPassword** skyddsnivå när du skapade ditt paket via SQL Server Data Tools, ange värdet för ditt lösenord i rutan **Kryptering lösenord.** Du kan också använda en hemlighet som lagras i nyckelvalvet som värde (se tidigare). Om du använde skyddsnivån **EncryptSensitiveWithUserKey** kan du skriva in dina känsliga värden igen i konfigurationsfiler eller på flikarna **SSIS-parametrar,** **Anslutningshanterare**eller **Egenskaper åsidosättningar** (se senare). 

   Om du använde skyddsnivån **EncryptAllWithUserKey** stöds den inte. Du måste konfigurera om paketet för att använda en annan skyddsnivå via SQL Server Data Tools eller `dtutil` kommandoradsverktyget. 
   
   För **loggningsnivå**väljer du ett fördefinierat omfång för loggning för paketkörningen. Markera kryssrutan **Anpassad** om du vill ange ditt anpassade loggningsnamn i stället. Om du vill logga paketkörningarna utöver att använda de standardloggleverantörer som kan anges i paketet anger du loggmappen genom att ange dess UNC-sökväg i **sökvägen loggning.** Om du till exempel lagrar dina loggar i Azure `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`Files är loggningssökvägen . En undermapp skapas i den här sökvägen för varje enskild paketkörning och namnges efter körningen SSIS-paketaktivitetskörnings-ID, där loggfiler genereras var femte minut. 
   
   Slutligen anger du autentiseringsuppgifterna för att komma åt loggmappen. Om du tidigare har angett värdena för dina autentiseringsuppgifter för paketåtkomst (se föregående) kan du återanvända dem genom att markera kryssrutan **Autentiseringsuppgifter för samma som paketåtkomst.** Annars anger du värdena för dina loggningsåtkomstautentiseringsuppgifter i rutorna **Domän,** **Användarnamn**och **Lösenord.** Om du till exempel lagrar loggarna i Azure `Azure`Files är `<storage account name>`domänen , `<storage account key>`användarnamnet är och lösenordet är . 

    Du kan också använda hemligheter som lagrats i nyckelvalvet som värden (se tidigare). Dessa autentiseringsuppgifter används för att lagra dina loggar. 
   
   För alla UNC-sökvägar som tidigare nämnts måste det fullständigt kvalificerade filnamnet vara färre än 260 tecken. Katalognamnet måste vara färre än 248 tecken.

1. På fliken **SSIS-parametrar** för aktiviteten Kör SSIS-paket, om din Azure-SSIS IR körs, **väljs SSISDB** som din paketplats och kryssrutan **Manuella poster** på fliken **Inställningar** avmarkeras visas de befintliga SSIS-parametrarna i det valda projektet eller paketet från SSISDB för att tilldela värden till dem. Annars kan du ange dem en efter en för att tilldela värden till dem manuellt. Kontrollera att de finns och har angetts korrekt för att paketkörningen ska lyckas. 
   
   Om du använde skyddsnivån **EncryptSensitiveWithUserKey** när du skapade paketet via SQL Server Data Tools and **File System (Package)** eller **File System (Project)** har valts som din paketplats, måste du också ange dina känsliga parametrar igen för att tilldela värden till dem i konfigurationsfiler eller på den här fliken. 
   
   När du tilldelar värden till dina parametrar kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, systemvariabler för Data Factory och parametrar eller variabler för datafabrikens pipeline. Du kan också använda hemligheter som lagrats i nyckelvalvet som värden (se tidigare).

   ![Ange egenskaper på fliken SSIS-parametrar](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Om din Azure-SSIS IR körs på fliken **Anslutningshanterare** för aktiviteten Kör SSIS-paket, om din Azure-SSIS IR körs, väljs **SSISDB** som din paketplats och kryssrutan **Manuella poster** på fliken **Inställningar** avmarkeras visas de befintliga anslutningsansvariga i det valda projektet eller paketet från SSISDB så att du kan tilldela värden till deras egenskaper. Annars kan du ange dem en efter en för att tilldela värden till deras egenskaper manuellt. Kontrollera att de finns och har angetts korrekt för att paketkörningen ska lyckas. 
   
   Om du använde skyddsnivån **EncryptSensitiveWithUserKey** när du skapade paketet via SQL Server Data Tools and **File System (Package)** eller **File System (Project)** har valts som din paketplats, måste du också ange egenskaperna för din känsliga anslutningshanterare för att tilldela värden till dem i konfigurationsfiler eller på den här fliken. 
   
   När du tilldelar värden till egenskaperna för anslutningshanteraren kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, systemvariabler för Data Factory och parametrar eller variabler för datafabrikspipeline. Du kan också använda hemligheter som lagrats i nyckelvalvet som värden (se tidigare).

   ![Ange egenskaper på fliken Anslutningshanterare](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. På fliken **Egenskapsidosättning** för aktiviteten Kör SSIS-paket anger du sökvägarna för befintliga egenskaper i det valda paketet en efter en för att tilldela värden till dem manuellt. Kontrollera att de finns och har angetts korrekt för att paketkörningen ska lyckas. Om du till exempel vill åsidosätta värdet för användarvariabeln anger du dess sökväg i följande format: `\Package.Variables[User::<variable name>].Value`. 
   
   Om du använde skyddsnivån **EncryptSensitiveWithUserKey** när du skapade paketet via SQL Server Data Tools and **File System (Package)** eller **File System (Project)** har valts som din paketplats, måste du också ange dina känsliga egenskaper igen för att tilldela värden till dem i konfigurationsfiler eller på den här fliken. 
   
   När du tilldelar värden till dina egenskaper kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, systemvariabler för Data Factory och parametrar eller variabler för datafabrikspipeline.

   ![Ange egenskaper på fliken Egenskapsöversidoringar](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   De värden som tilldelas i konfigurationsfiler och på fliken **SSIS-parametrar** kan åsidosättas med hjälp av flikarna **Anslutningshanterare** eller **Egenskaper åsidosättningar.** De värden som tilldelas på fliken **Anslutningshanterare** kan också åsidosättas med hjälp av fliken **Egenskapsidosättning.**

1. Om du vill validera pipelinekonfigurationen väljer du **Validera** i verktygsfältet. Om du vill stänga **>>** **pipelineverifieringsrapporten**väljer du .

1. Om du vill publicera pipelinen till Data Factory väljer du **Publicera alla**. 

### <a name="run-the-pipeline"></a>Köra en pipeline
I det här steget utlöser du en pipeline-körning. 

1. Om du vill utlösa en pipelinekörning väljer du **Utlösare** i verktygsfältet och väljer **Utlösare nu**. 

   ![Utlös nu](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du ser pipeline-körningen och dess status tillsammans med annan information, till exempel **körstartstiden.** Välj **Uppdatera** för att uppdatera vyn.

   ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Välj länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). Du ser bara en aktivitetskörning eftersom pipelinen bara har en aktivitet. Det är aktiviteten Kör SSIS-paket.

   ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Kör följande fråga mot SSISDB-databasen i SQL-servern för att kontrollera att paketet kördes. 

   ```sql
   select * from catalog.executions
   ```

   ![Verifiera paketkörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Du kan också hämta SSISDB-körnings-ID från utdata från pipelineaktivitetskörningen och använda ID:t för att kontrollera mer omfattande körningsloggar och felmeddelanden i SQL Server Management Studio.

   ![Hämta körnings-ID: et.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägga pipelinen med en utlösare

Du kan också skapa en schemalagd utlösare för pipelinen så att pipelinen körs enligt ett schema, till exempel varje timme eller dagligen. Ett exempel finns i [Skapa en datafabrik - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Kör ett paket med PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en kör SSIS-paketaktivitet som kör ditt SSIS-paket. 

Installera de senaste Azure PowerShell-modulerna genom att följa steg-för-steg-instruktioner i [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Skapa en datafabrik med Azure-SSIS IR
Du kan antingen använda en befintlig datafabrik som redan har Azure-SSIS IR-etablerat eller skapa en ny datafabrik med Azure-SSIS IR. Följ steg-för-steg-instruktionerna i [självstudiekursen: Distribuera SSIS-paket till Azure via PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en körning ssis-paketaktivitet 
I det här steget skapar du en pipeline med en kör SSIS-paketaktivitet. Aktiviteten kör ditt SSIS-paket. 

1. Skapa en JSON-fil med namnet *RunSSISPackagePipeline.json* i mappen *C:\ADF\RunSSISPackage* med innehåll som liknar följande exempel.

   > [!IMPORTANT]
   > Ersätt objektnamn, beskrivningar och banor, egenskaps- eller parametervärden, lösenord och andra variabelvärden innan du sparar filen. 
    
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

   Om du vill köra paket som lagras i filsystem, filresurser eller Azure-filer anger du värdena för egenskaperna för paketet och loggplatsen enligt följande:

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

   Om du vill köra paket i projekt som lagras i filsystem, filresurser eller Azure-filer anger du värdena för egenskapen paketplats enligt följande:

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

   Om du vill köra inbäddade paket anger du värdena för egenskapen paketplats enligt följande:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. I Azure PowerShell växlar du till mappen *C:\ADF\RunSSISPackage.*

3. Om du vill skapa pipelinen **RunSSISPackagePipeline**kör du cmdleten **Set-AzDataFactoryV2Pipeline.**

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Här är exempelutdata:

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

Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera eller klistra in följande skript i PowerShell-fönstret och välj Retur. 

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

Du kan också övervaka pipelinen med hjälp av Azure-portalen. Stegvisa instruktioner finns i [Övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägga pipelinen med en utlösare
I föregående steg körde du pipelinen på begäran. Du kan också skapa en schemautlösare för att köra pipelinen enligt ett schema, till exempel varje timme eller dagligen.

1. Skapa en JSON-fil med namnet *MyTrigger.json* i mappen *C:\ADF\RunSSISPackage* med följande innehåll: 
        
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
    
1. I Azure PowerShell växlar du till mappen *C:\ADF\RunSSISPackage.*
1. Kör **cmdleten Set-AzDataFactoryV2Trigger,** vilket skapar utlösaren. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Som standard är utlösaren i stoppat tillstånd. Starta utlösaren genom att köra cmdleten **Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Bekräfta att utlösaren startas genom att köra cmdleten **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Kör följande kommando efter nästa timme. Om den aktuella tiden till exempel är 15:25 UTC kör du kommandot vid 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Kör följande fråga mot SSISDB-databasen i SQL-servern för att kontrollera att paketet kördes. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Nästa steg
Se följande blogginlägg:
- [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i Azure Data Factory-pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
