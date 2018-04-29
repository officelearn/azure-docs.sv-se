---
title: Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell | Microsoft Docs
description: Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera och köra ett Azure Stream Analytics-jobb.
services: stream-analytics
keywords: Stream analytics, Cloud jobs, Azure PowerShell, job input, job output, job transformation
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 8a1036531ea0e7c1426224bc4d42c83e9049cabf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser med hjälp av PowerShell-cmdletar eller -skript. Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera och köra ett Azure Stream Analytics-jobb.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).  

* Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Hitta versionen som är installerad på den lokala datorn genom att köra `Get-Module -ListAvailable AzureRM`. Om du behöver installera eller uppgradera kan du läsa artikeln [Installera Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Scenariot i den här artikeln beskriver hur du läser data från bloblagringen, transformerar data och skriver dessa data tillbaka till en annan behållare i samma bloblagring.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och ange dina autentiseringsuppgifter för Azure i popup-webbläsaren. När du har loggat in väljer du, om du har flera prenumerationer, den prenumeration som du vill använda för den här snabbstarten genom att köra följande cmdletar. Ersätt <your subscription> med namnet på prenumerationen:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# Select the Azure subscription you want to use to create the resource group.
Get-AzureRmSubscription `
  -SubscriptionName “<your subscription>” | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet bör du förbereda de data som har konfigurerats som indata för jobbet. Kör följande steg för att förbereda de indata som krävs för jobbet: 

1. Ladda ned [exempelsensordata](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) från GitHub.  

2. Skapa ett allmänt standardlagringskonto med LRS-replikering med hjälp av cmdleten [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  

3. Hämta lagringskontokontexten som definierar det lagringskonto som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger. I det här exemplet skapas ett lagringskonto med namnet mystorageaccount med lokalt redundant lagring (LRS) och blobkryptering (aktiverat som standard).  

4. Därefter skapar du en behållare med hjälp av [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), anger behörigheten till 'blob' för att tillåta offentlig åtkomst för filerna och laddar upp de [exempelsensordata](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) som du laddade ned tidigare. 

De här stegen uppnås genom att köra följande PowerShell-skript:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Skapa ett Stream Analytics-jobb med cmdleten [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, resursgruppens namn och jobbdefinitionen som parametrar. Jobbnamnet kan vara valfritt eget namn som identifierar jobbet. Stream Analytics-jobbets namn får enbart innehålla alfanumeriska tecken, bindestreck och understreck och måste vara mellan 3 och 63 tecken långt. Jobbdefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att skapa ett jobb. Skapa en fil med namnet ”JobDefinition.json” på den lokala datorn och lägg till följande JSON-data i den:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Därefter kör du cmdleten New-AzureRMStreamAnalyticsJob och ersätter värdet för variabeln jobDefinitionFile med sökvägen där du har sparat JSON-filen med jobbdefinitionen. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Konfigurera indata för jobbet

Lägg till indata för jobbet med hjälp av cmdleten [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, jobbindatanamnet, resursgruppens namn och jobbindatadefinitionen som parametrar. Jobbindatadefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets indata. I det här exemplet skapar du en bloblagring som indata. Skapa en fil med namnet ”JobInputDefinition.json” på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för **accountKey** med lagringskontots åtkomstnyckel som är det värde som lagras i värdet $storageAccountKey. 

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
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
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

Därefter kör du cmdleten New-AzureRMStreamAnalyticsInput och ersätter värdet för variabeln jobDefinitionFile med sökvägen där du har sparat JSON-filen med jobbindatadefinitionen. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Konfigurera utdata för jobbet

Lägg till utdata för jobbet med hjälp av cmdleten [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, jobbutdatanamnet, resursgruppens namn och jobbutdatadefinitionen som parametrar. Jobbutdatadefinitionen är en JSON-fil som innehåller de egenskaper som krävs för att konfigurera jobbets utdata. I det här exemplet skapar du en bloblagring som utdata. Skapa en fil med namnet ”JobOutputDefinition.json” på den lokala datorn och lägg till följande JSON-data i den. Ersätt värdet för **accountKey** med lagringskontots åtkomstnyckel som är det värde som lagras i värdet $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
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

Därefter kör du cmdleten New-AzureRMStreamAnalyticsOutput och ersätter värdet för variabeln jobOutputDefinitionFile med sökvägen där du har sparat JSON-filen med jobbutdatadefinitionen. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Lägg till en transformation för jobbet med hjälp av cmdleten [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, jobbtransformationsnamnet, resursgruppens namn och jobbtransformationsdefinitionen som parametrar. Skapa en fil med namnet ”JobTransformationDefinition.json” på den lokala datorn och lägg till följande JSON-data i den. JSON-filen innehåller en frågeparameter som definierar transformationsfrågan:

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

Därefter kör du cmdleten New-AzureRMStreamAnalyticsTransformation och ersätter värdet för variabeln jobTransformationDefinitionFile med sökvägen där du har sparat JSON-filen med jobbtransformationsdefinitionen. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

Starta jobbet med hjälp av cmdleten [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Den här cmdleten tar jobbnamnet, resursgruppens namn, startläget för utdata och starttiden som parametrar. OutputStartMode accepterar värdet JobStartTime, CustomTime eller LastOutputEventTime. Mer information om vad de här värdena refererar till finns i avsnittet om [parametrar](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) i PowerShell-dokumentationen. I det här exemplet kan du ange läge som CustomTime och ange ett värde för OutputStartTime. 

Välj en dag före den dag då du laddade upp filen till bloblagring som tidsvärde eftersom den tid då filen laddades upp är tidigare än aktuell tidpunkt. När du har kört följande cmdlet returnerar den ”True” som utdata om jobbet startar. En behållare med namnet ”myoutputcontainer” skapas på lagringskontot med transformerade data. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömningsenheter som förbrukas av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett enkelt Stream Analytics-jobb. Om du vill se hur du konfigurerar andra indatakällor och utför realtidsidentifiering fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Identifiering av bedrägerier i realtid med hjälp av Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
