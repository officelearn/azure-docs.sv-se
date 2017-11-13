---
title: Transformera data med Hive i Azure Virtual Network | Microsoft Docs
description: "Den här självstudiekursen innehåller stegvisa instruktioner för hur du transformerar data genom att använda en Hive-aktivitet i Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: shengc
ms.openlocfilehash: b8c30a2fd68178ddd2bfb3ff079c47ba00928855
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformera data i Azure Virtual Network med en Hive-aktivitet i Azure Data Factory

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Den här självstudien

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som transformerar data med en Hive-aktivitet på ett HDInsight-kluster som finns i Azure Virtual Network. I de här självstudierna går du igenom följande steg:

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa och konfigurera Integration Runtime med egen värd
> * Skapa och distribuera länkade tjänster.
> * Skapa och distribuera en pipeline som innehåller en Hive-aktivitet.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen 
> * verifiera utdata. 


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav
- **Azure Storage-konto**. Du skapar ett hive-skript och överför det till Azure Storage. Hive-skriptets utdata lagras på det här lagringskontot. I det här exemplet använder HDInsight-klustret det här Azure Storage-kontot som primär lagring. 
- **Azure Virtual Network.** Om du inte har något virtuellt Azure-nätverk skapar du det genom att följa [de här instruktionerna](../virtual-network/virtual-network-get-started-vnet-subnet.md). I det här exemplet är HDInsight i ett virtuellt Azure-nätverk. Här är en exempelkonfiguration av Azure Virtual Network. 

    ![Skapa det virtuella nätverket](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **HDInsight-kluster.** Skapa ett HDInsight-kluster och anslut det till det virtuella nätverket som du skapade i föregående steg genom att följa stegen i den här artikeln: [Extend Azure HDInsight using an Azure Virtual Network](../hdinsight/hdinsight-extend-hadoop-virtual-network.md) (Utöka HDInsight med ett Azure Virtual Network). Här är en exempelkonfiguration av HDInsight i ett virtuellt nätverk. 

    ![HDInsight i ett virtuellt nätverk](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Överföra Hive-skriptet till ditt Blob Storage-konto

1. Skapa en Hive SQL-fil med namnet **hivescript.hql** med följande innehåll:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Skapa en behållare med namnet **adftutorial** i Azure Blob Storage om den inte finns.
3. Skapa en mapp med namnet `hivescripts`.
4. Överför filen `hivescript.hql` till undermappen `hivescripts`.

 

## <a name="create-a-data-factory"></a>Skapa en datafabrik


1. Ange variabler en i taget.

    ```powershell
    $subscriptionID = "<subscription ID>" # Your Azure subscription ID
    $resourceGroupName = "ADFTutorialResourceGroup" # Name of the resource group
    $dataFactoryName = "MyDataFactory09142017" # Globally unique name of the data factory
    $pipelineName = "MyHivePipeline" # Name of the pipeline
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" # make it a unique name. 
    ```
2. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till. För närvarande kan du endast skapa datafabriker i Data Factory V2 i regionerna USA, östra; USA; östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

    Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in i Azure Portal:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
    Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Skapa resursgruppen: ADFTutorialResourceGroup om den inte redan finns i din prenumeration. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Skapa datafabriken. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Kör följande kommando för att se utdata: 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Skapa IR med egen värd
I det här avsnittet skapar du en Integration Runtime med egen värd och kopplar den till en virtuell Azure-dator i samma Azure Virtual Network som innehåller ditt HDInsight-kluster.

1. Skapa Integration Runtime med egen värd. Använd ett unikt namn om det finns en annan Integration Runtime med samma namn.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Detta kommando skapar en logisk registrering av din Integration Runtime med egen värd. 
2. Använd PowerShell för att hämta autentiseringsnycklar för att registrera Integration Runtime med egen värd. Kopiera någon av nycklarna för att registrera Integration Runtime med egen värd.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Här är exempel på utdata: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Anteckna värdet **AuthKey1** utan citattecken. 
3. Skapa en virtuell Azure-dator och koppla den till samma virtuella nätverk som innehåller ditt HDInsight-kluster. Läs mer i informationen om att [skapa virtuella datorer](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vms). Koppla dem till ett Azure Virtual Network. 
4. Ladda ned [integration runtime med egen värd](https://www.microsoft.com/download/details.aspx?id=39717) till den virtuella Azure-datorn. Använd autentiseringsnyckeln som hämtades i föregående steg för att manuellt registrera integration runtime med egen värd. 

   ![Registrera Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   När integration runtime med egen värd har registrerats ser du följande meddelande: ![Registered successfully](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png) (Registrerat)

   Du ser följande sida när noden är ansluten till molntjänsten: ![Node is connected](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png) (Noden är ansluten)

## <a name="author-linked-services"></a>Skapa länkade tjänster

Du skapar och distribuerar två länkade tjänster i det här avsnittet:
- Den länkade Azure Storage-tjänsten som länkar ditt Azure Storage-konto till datafabriken. Den här lagringen är den som primärt används av ditt HDInsight-kluster. I det här fallet använder vi även det här Azure Storage-kontot för att lagra Hive-skriptet och dess utdata.
- En länkad HDInsight-tjänst. Azure Data Factory skickar Hive-skriptet till det här HDInsight-klustret för körning.

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

Skapa en JSON-fil med önskat redigeringsprogram, kopiera följande JSON-definition för en länkad Azure Storage-tjänst och spara filen som **MyStorageLinkedService.json**.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Ersätt **&lt;accountname&gt; och &lt;accountkey&gt;** med namnet och nyckeln för ditt Azure-lagringskonto.

### <a name="hdinsight-linked-service"></a>Länkad HDInsight-tjänst

Skapa en JSON-fil med önskat redigeringsprogram, kopiera följande JSON-definition för en länkad Azure HDInsight-tjänst och spara filen som **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Uppdatera värden för följande egenskaper i definitionen för den länkade tjänsten:

- **userName**. Namnet på användaren för klusterinloggningen som du angav när du skapade klustret. 
- **password**. Ange lösenordet för användaren.
- **clusterUri**. Ange URL-adressen till ditt HDInsight-kluster i formatet  https://<clustername>.azurehdinsight.net.  Den här artikeln förutsätter att du har åtkomst till klustret via internet. Till exempel att du kan ansluta till klustret i `https://clustername.azurehdinsight.net`. Den här adressen använder den offentliga gatewayen, som inte är tillgänglig om du har använt nätverkssäkerhetsgrupper (NSG:er) eller användardefinierade vägar (UDR:er) för att begränsa åtkomst från internet. För att Data Factory ska kunna skicka jobb till HDInsight-klustret i Azure Virtual Network måste du konfigurera ditt Azure Virtual Network så att URL-adressen kan matchas med gatewayens privata IP-adress som används av HDInsight.

  1. Från Azure-portalen öppnar du det virtuella nätverket som HDInsight finns i. Öppna nätverksgränssnittet med namnet som börjar med `nic-gateway-0`. Skriv ned dess privata IP-adress. Till exempel 10.6.0.15. 
  2. Om din Azure Virtual Network har en DNS-server uppdaterar du DNS-posten så HDInsight-klustrets URL `https://<clustername>.azurehdinsight.net` kan matchas mot `10.6.0.15`. Detta är den rekommenderade metoden. Om du inte har någon DNS-server i ditt virtuella Azure-nätverk kan du tillfälligt lösa detta genom att redigera värdfilen (C:\Windows\System32\drivers\etc) för alla virtuella datorer som är registrerade som IR-noder med egen värd genom att lägga till en post så här: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

Växla till den mapp där du skapade JSON-filerna och kör följande kommando för att distribuera de länkade tjänsterna: 


```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"

Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDILinkedService" -File "MyHDILinkedService.json"
```

## <a name="author-a-pipeline"></a>Skapa en pipeline
I det här steget kan du skapa en ny pipeline med en Hive-aktivitet. Aktiviteten kör Hive-skript för att returnera data från en exempeltabell och spara dem till en sökväg som du har definierat. Skapa en JSON-fil med önskat redigeringsprogram, kopiera följande JSON-definition för en pipelinedefinition och spara filen som **MyHiveOnDemandPipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

Observera följande punkter:

- **scriptPath** pekar på sökvägen till Hive-skriptet i Azure Storage-kontot du använde för MyStorageLinkedService. Sökvägen är skiftlägeskänslig.
- **Utdata** är ett argument som används i Hive-skriptet. Använd formatet `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` för att peka på en befintlig mapp i Azure Storage. Sökvägen är skiftlägeskänslig. 

Växla till den mapp där du skapade JSON-filerna och kör följande kommando för att distribuera dessa länkade pipelines: 


```powershell
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Starta pipelinen 

1. Starta en pipelinekörning. Den samlar även in pipelinekörningens ID för kommande övervakning.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. Kör följande skript för att kontinuerligt kontrollera pipelinekörningens status tills den är klar.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }
    
    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

   Här är utdata för exempelkörningen:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Titta i mappen `outputfolder` efter filen som skapades som Hive-frågeresultat. Det bör se ut så här: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa och konfigurera Integration Runtime med egen värd
> * Skapa och distribuera länkade tjänster.
> * Skapa och distribuera en pipeline som innehåller en Hive-aktivitet.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen 
> * verifiera utdata. 

Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Förgrening och länkning av Data Factory-kontrollflöde](tutorial-control-flow.md)



