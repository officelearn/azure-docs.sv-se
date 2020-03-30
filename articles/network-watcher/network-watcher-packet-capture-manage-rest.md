---
title: Hantera paketinsamlingar med Azure Network Watcher - REST API | Microsoft-dokument
description: På den här sidan beskrivs hur du hanterar paketinsamlingsfunktionen i Network Watcher med Azure REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 5199cf95452f93db2c2dd747fcabc67a6722d31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840901"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Hantera paketinsamlingar med Azure Network Watcher med Azure REST API

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [REST-API för Azure](network-watcher-packet-capture-manage-rest.md)

Med network watcher-paketfånget kan du skapa insamlingssessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingssessionen för att säkerställa att du bara samlar in den trafik du vill ha. Paketinsamling hjälper till att diagnostisera nätverksavvikelser både reaktivt och proaktivt. Andra användningsområden inkluderar att samla in nätverksstatistik, få information om nätverksintrång, att felsöka klient-server kommunikation och mycket mer. Genom att fjärrutlösa paketinfångningar, underlättar den här funktionen bördan av att köra en paketfångst manuellt och på önskad maskin, vilket sparar värdefull tid.

Den här artikeln tar dig igenom de olika hanteringsuppgifter som för närvarande är tillgängliga för paketinsamling.

- [**Hämta en paketfångst**](#get-a-packet-capture)
- [**Lista alla paketinfång**](#list-all-packet-captures)
- [**Fråga status för en paketfångst**](#query-packet-capture-status)
- [**Starta en paketfångst**](#start-packet-capture)
- [**Stoppa en paketfångst**](#stop-packet-capture)
- [**Ta bort en paketfångst**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här fallet anropar du API:et för nätverksbevakare för att köra IP Flow Verify. ARMclient används för att anropa REST API med PowerShell. ARMClient finns på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

> Paketfångsten kräver `AzureNetworkWatcherExtension`ett tillägg för den virtuella datorn . För att installera tillägget på en Windows VM besök [Azure Network Watcher Agent virtuell dator tillägg för Windows](../virtual-machines/windows/extensions-nwa.md) och för Linux VM besök Azure Network [Watcher Agent virtuell dator tillägg för Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Hämta en virtuell dator

Kör följande skript för att returnera en virtuell dator. Den här informationen behövs för att starta en paketfångst.

Följande kod behöver variabler:

- **subscriptionId** - Prenumerations-ID kan också hämtas med **Cmdlet Get-AzSubscription.**
- **resourceGroupName** - Namnet på en resursgrupp som innehåller virtuella datorer.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Från följande utdata används id:t för den virtuella datorn i nästa exempel.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Hämta en paketfångst

Följande exempel hämtar status för en enda paketfångst

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Följande svar är exempel på ett typiskt svar som returneras när du frågar status för en paketfångst.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Lista alla paketinfång

I följande exempel hämtas alla paketfångstsessioner i en region.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Följande svar är ett exempel på ett typiskt svar som returneras när alla paketinsamlingar

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Status för insamling av frågepaket

I följande exempel hämtas alla paketfångstsessioner i en region.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Följande svar är ett exempel på ett typiskt svar som returneras när du frågar status för en paketfångst.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Starta pakethämtning

I följande exempel skapas en paketfångst på en virtuell dator.  Exemplet är parameteriserat för att möjliggöra flexibilitet när du skapar ett exempel.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Stoppa paketfångst

I följande exempel stoppas en paketfångst på en virtuell dator.  Exemplet är parameteriserat för att möjliggöra flexibilitet när du skapar ett exempel.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Ta bort paketinsamling

I följande exempel tas en paketfångst bort på en virtuell dator.  Exemplet är parameteriserat för att möjliggöra flexibilitet när du skapar ett exempel.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Om du tar bort en paketinsamling tas inte filen bort i lagringskontot

## <a name="next-steps"></a>Nästa steg

Instruktioner om hur du hämtar filer från azure storage-konton finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ett annat verktyg som kan användas är Storage Explorer. Mer information om Storage Explorer finns här på följande länk: [Storage Explorer](https://storageexplorer.com/)

Lär dig hur du automatiserar paketinsamlingar med aviseringar för virtuella datorer genom att visa [Skapa en aviseringsutlöst paketfångst](network-watcher-alert-triggered-packet-capture.md)













