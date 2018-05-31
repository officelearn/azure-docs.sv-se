---
title: Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell
description: Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera och köra ett Azure Stream Analytics-jobb.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212440"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser med hjälp av PowerShell-cmdletar eller -skript. Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera och köra ett Azure Stream Analytics-jobb. 

Exempeljobbet läser strömmande data från en blob i Azure blobblagring. Indata-filen som används i denna Snabbstart innehåller statiska data som endast är för illustration. I ett verkligt scenario använder du strömningsindata för en Stream Analytics-jobb. Därefter transformerar jobbet data med Stream Analytics-frågespråket för att beräkna medeltemperaturen när den är över 100 °. Slutligen skriver den resulterande utdatahändelser till en annan fil. 

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).  

* Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Hitta versionen som är installerad på den lokala datorn genom att köra `Get-Module -ListAvailable AzureRM`. Om du behöver installera eller uppgradera kan du läsa artikeln [Installera Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och ange dina autentiseringsuppgifter för Azure i webbläsarens popup-fönster:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

När du har loggat in väljer du, om du har flera prenumerationer, den prenumeration som du vill använda för den här snabbstarten genom att köra följande cmdletar. Ersätt <your subscription name> med namnet på prenumerationen:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet förbereder du de data som har konfigurerats som indata för jobbet.

1. Ladda ned [exempelsensordata](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) från GitHub. Högerklicka på länken och välj **Spara länken som...**  eller **Spara mål som**.

2. Följande PowerShell-kodblock utför flera kommandon som förbereder de indata som krävs för jobbet. Läs avsnitten för att förstå koden. 

   1. Skapa ett allmänt standardlagringskonto med hjälp av cmdleten [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  I det här exemplet skapas ett lagringskonto med namnet mystorageaccount med lokalt redundant lagring (LRS) och blobkryptering (aktiverat som standard).  

   2. Hämta lagringskontokontexten `$storageAccount.Context` som definierar det lagringskonto som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger. 

   3. Skapa en lagringsbehållare med [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) och överför de [sensorexempeldata](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) som du laddade ned tidigare. 

   4. Kopiera lagringsnyckeln som koden ger och klistra in nyckeln i JSON-filerna för att kunna skapa indata och utdata för det strömmande jobbet vid ett senare tillfälle.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Skapa ett Stream Analytics-jobb med cmdleten [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, resursgruppens namn och jobbdefinitionen som parametrar. Jobbnamnet kan vara ett eget namn som identifierar jobbet. Det får enbart innehålla alfanumeriska tecken, bindestreck och understreck och måste vara mellan 3 och 63 tecken långt. Jobbdefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att skapa ett jobb. Skapa en fil med namnet `JobDefinition.json` på den lokala datorn och lägg till följande JSON-data i den:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Kör sedan cmdleten `New-AzureRmStreamAnalyticsJob`. Ersätt värdet för variabeln `jobDefinitionFile` med sökvägen där du har sparat jobbdefinitionens JSON-fil. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Konfigurera indata för jobbet

Lägg till indata för jobbet med hjälp av cmdleten [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, jobbindatanamnet, resursgruppens namn och jobbindatadefinitionen som parametrar. Jobbets indatadefinition är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets indata. I det här exemplet skapar du en bloblagring som indata. 

Skapa en fil med namnet `JobInputDefinition.json` på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för `accountKey` med åtkomstnyckeln för ditt lagringskonto, vilket är det värde som lagras i $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Kör sedan cmdleten `New-AzureRmStreamAnalyticsInput` och ersätt värdet för variabeln `jobDefinitionFile` med sökvägen där du har sparat jobbdefinitionens JSON-fil för indatan. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Konfigurera utdata för jobbet

Lägg till utdata för jobbet med hjälp av cmdleten [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, jobbutdatanamnet, resursgruppens namn och jobbutdatadefinitionen som parametrar. Jobbets utdatadefinition är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets utdata. Det här exemplet använder bloblagring som utdata. 

Skapa en fil med namnet `JobOutputDefinition.json` på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för `accountKey` med åtkomstnyckeln för ditt lagringskonto, vilket är det värde som lagras i $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Kör sedan cmdleten `New-AzureRmStreamAnalyticsOutput`. Ersätt värdet för variabeln `jobOutputDefinitionFile` med sökvägen där du har sparat jobbdefinitionens JSON-fil för utdata. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Lägg till en transformation för jobbet med hjälp av cmdleten [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, jobbtransformationsnamnet, resursgruppens namn och jobbtransformationsdefinitionen som parametrar. Skapa en fil med namnet `JobTransformationDefinition.json` på den lokala datorn och lägg till följande JSON-data i den. JSON-filen innehåller en frågeparameter som definierar transformationsfrågan:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Kör sedan cmdleten `New-AzureRmStreamAnalyticsTransformation`. Ersätt värdet för variabeln `jobTransformationDefinitionFile` med sökvägen där du har sparat jobbdefinitionens JSON-fil för transformeringen. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Starta jobbet med hjälp av cmdleten [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, resursgruppens namn, startläget för utdata och starttiden som parametrar. `OutputStartMode` godkänner värden för `JobStartTime`, `CustomTime` eller `LastOutputEventTime`. Mer information om vad dessa värden refererar till finns i avsnittet om [parametrar](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) i PowerShell-dokumentationen. I det här exemplet anger du läget som `CustomTime` och anger ett värde för `OutputStartTime`. 

Som tidsvärde väljer du `2018-01-01`. Det här startdatumet är valt eftersom det kommer före tidsstämpeln för händelsen från exempeldata. När du har kört följande cmdlet returnerar den `True` som utdata om jobbet startar. En utdatamapp skapas i lagringsbehållaren med de data som har transformerats. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömningsenheter som förbrukas av jobbet. Om du planerar att använda jobbet i framtiden kan du låta bli att ta bort det och stoppa det just nu. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett enkelt Stream Analytics-jobb. Om du vill se hur du konfigurerar andra indatakällor och utför realtidsidentifiering fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Identifiering av bedrägerier i realtid med hjälp av Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
