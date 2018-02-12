---
title: "Läs NSG flödet loggar | Microsoft Docs"
description: "Den här artikeln visar hur du Parsar flödet NSG-loggar"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 58474286352ff3f00b31e65a565c2b64a656a177
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="read-nsg-flow-logs"></a>Läs NSG flödet loggar

Lär dig mer om att läsa NSG flödet loggar poster med PowerShell.

NSG flödet loggfilerna lagras i ett lagringskonto i [blockblobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs.md#about-block-blobs). Blockblobbar består av mindre block. Varje logg är en separat block-blob som skapas varje timme. Nya loggar genereras varje timme, loggarna uppdateras med nya poster med några minuters mellanrum med den senaste informationen. Lär dig hur de ska läsa delar av flödet loggar i den här artikeln.

## <a name="scenario"></a>Scenario

I följande scenario har du en exempel-flöde logg som är lagrad i ett lagringskonto. vi gå igenom hur du selektivt kan läsa de senaste händelserna i flödet NSG-loggar. I den här artikeln ska vi använda PowerShell, men de begrepp som diskuteras i artikeln är inte begränsade till programmeringsspråket och gäller för alla språk som stöds av Azure Storage-API: er

## <a name="setup"></a>Konfiguration

Du måste ha nätverket grupp flöda säkerhetsloggning aktiverad på en eller flera Nätverkssäkerhetsgrupper i ditt konto innan du börjar. Anvisningar om hur du aktiverar Network Security flöda loggar, finns i följande artikel: [introduktion till flödet loggning för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Hämta lista över blockerade

Följande PowerShell ställer in variabler som behövs för att fråga NSG flödet loggen blob och visa block i den [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azurestorage-8.1.3) blockblob. Uppdatera skriptet för att innehålla giltiga värden för din miljö.

```powershell
# The SubscriptionID to use
$subscriptionId = "00000000-0000-0000-0000-000000000000"

# Resource group that contains the Network Security Group
$resourceGroupName = "<resourceGroupName>"

# The name of the Network Security Group
$nsgName = "NSGName"

# The storage account name that contains the NSG logs
$storageAccountName = "<storageAccountName>" 

# The date and time for the log to be queried, logs are stored in hour intervals.
[datetime]$logtime = "06/16/2017 20:00"

# Retrieve the primary storage account key to access the NSG logs
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]

# Setup a new storage context to be used to query the logs
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Container name used by NSG flow logs
$ContainerName = "insights-logs-networksecuritygroupflowevent"

# The MAC Address of the Network Interface
$macAddress = "000D3AFA8650"

# Name of the blob that contains the NSG flow log
$BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${nsgName}/y=$($logtime.Year)/m=$(($logtime).ToString("MM"))/d=$(($logtime).ToString("dd"))/h=$(($logtime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

# Gets the storage blog
$Blob = Get-AzureStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

# Gets the block blog of type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from the storage blob
$CloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

# Stores the block list in a variable from the block blob.
$blockList = $CloudBlockBlob.DownloadBlockList()
```

Den `$blockList` variabeln returnerar en lista över blocken i blob. Varje blockblob innehåller minst två block.  Det första blocket som har en längd på `21` byte blocket innehåller inledande hakparenteser i json-loggen. Andra blocket är avslutande hakparenteser och har en längd på `9` byte.  Som du ser i följande exempel-loggen innehåller sju poster, som en enskild post. Alla nya poster i loggen har lagts till i slutet innan det sista blocket.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Läsa blockblob

Nästa vi behöver läsa den `$blocklist` variabeln för att hämta data. I det här exemplet vi gå igenom blocklist läsa byte från varje block och artikel dem i en matris. Vi använder den [DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadrangetobytearray?view=azurestorage-8.1.3#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadRangeToByteArray_System_Byte___System_Int32_System_Nullable_System_Int64__System_Nullable_System_Int64__Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metod för att hämta data.

```powershell
# Set the size of the byte array to the largest block
$maxvalue = ($blocklist | measure Length -Maximum).Maximum

# Create an array to store values in
$valuearray = @()

# Define the starting index to track the current block being read
$index = 0

# Loop through each block in the block list
for($i=0; $i -lt $blocklist.count; $i++)
{

# Create a byte array object to story the bytes from the block
$downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

# Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
$CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index+3,$($blockList[$i].Length-1)) | Out-Null

# Increment the index by adding the current block length to the previous index
$index = $index + $blockList[$i].Length

# Retrieve the string from the byte array

$value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

# Add the log entry to the value array
$valuearray += $value
}
```

Nu den `$valuearray` matris innehåller strängvärdet för varje block. Om du vill verifiera posten hämta andra till det sista värdet från matrisen genom att köra `$valuearray[$valuearray.Length-2]`. Vi vill inte det senaste värdet är bara avslutande hakparentes.

Resultatet av detta värde visas i följande exempel:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Det här scenariot är ett exempel på hur du läser posterna i NSG flödet loggar utan att analysera hela loggen. Du kan läsa nya poster i loggen som de är skrivna med hjälp av block-ID eller spåra längden på datablock som lagras i blockblob. På så sätt kan du läsa endast nya poster.


## <a name="next-steps"></a>Nästa steg

Besök [visualisera Azure Network Watcher NSG flödet loggar med öppen källkod verktyg](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) vill veta mer om andra sätt att visa NSG flödet loggar.

Läs mer om storage-blobbar finns: [Azure Functions Blob storage bindningar](../azure-functions/functions-bindings-storage-blob.md)
