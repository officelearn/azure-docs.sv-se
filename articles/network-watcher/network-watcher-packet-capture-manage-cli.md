---
title: Hantera paketinsamlingar med Azure Network Watcher - Azure CLI | Microsoft-dokument
description: På den här sidan beskrivs hur du hanterar paketinsamlingsfunktionen i Network Watcher med Hjälp av Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7a69610d1ac176354a9d7e388a12ccc7f064d848
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382723"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Hantera paketinsamlingar med Azure Network Watcher med Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [REST-API för Azure](network-watcher-packet-capture-manage-rest.md)

Med network watcher-paketfånget kan du skapa insamlingssessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingssessionen för att säkerställa att du bara samlar in den trafik du vill ha. Paketinsamling hjälper till att diagnostisera nätverksavvikelser både reaktivt och proaktivt. Andra användningsområden inkluderar att samla in nätverksstatistik, få information om nätverksintrång, att felsöka klient-server kommunikation och mycket mer. Genom att fjärrutlösa paketinfångningar, underlättar den här funktionen bördan av att köra en paketfångst manuellt och på önskad maskin, vilket sparar värdefull tid.

Om du vill utföra stegen i den här artikeln måste du [installera Azure Command-Line Interface för Mac, Linux och Windows (Azure CLI)](/cli/azure/install-azure-cli).

Den här artikeln tar dig igenom de olika hanteringsuppgifter som för närvarande är tillgängliga för paketinsamling.

- [**Starta en paketfångst**](#start-a-packet-capture)
- [**Stoppa en paketfångst**](#stop-a-packet-capture)
- [**Ta bort en paketfångst**](#delete-a-packet-capture)
- [**Ladda ned en paketfångst**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

- En instans av Network Watcher i den region som du vill skapa en paketfångst
- En virtuell dator med paketfångstenstillägget aktiverat.

> [!IMPORTANT]
> Paketfångsten kräver att en agent körs på den virtuella datorn. Agenten installeras som ett tillägg. Instruktioner om tillägg till virtuella datorer finns i [tillägg och funktioner för virtuella datorer](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Installera VM-tillägg

### <a name="step-1"></a>Steg 1

Kör `az vm extension set` kommandot för att installera paketfångstensagenten på den virtuella gästdatorn.

För virtuella Windows-datorer:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

För virtuella Linux-datorer:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Steg 2

Om du vill vara säkra `vm extension show` på att agenten är installerad kör du kommandot och skickar det till resursgruppen och namnet på den virtuella datorn. Kontrollera den resulterande listan för att säkerställa att agenten är installerad.

För virtuella Windows-datorer:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

För virtuella Linux-datorer:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Följande exempel är ett exempel på svaret från att köra`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Starta en paketfångst

När föregående steg är klara installeras paketfångningsagenten på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Hämta ett lagringskonto. Det här lagringskontot används för att lagra paketinsamlingsfilen.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Steg 2

Nu är du redo att skapa en paketfångst.  Låt oss först undersöka de parametrar som du kanske vill konfigurera. Filter är en sådan parameter som kan användas för att begränsa de data som lagras av paketinsamlingen. I följande exempel ställs in en paketfångst med flera filter.  De tre första filtren samlar endast utgående TCP-trafik från lokal IP 10.0.0.3 till målportarna 20, 80 och 443.  Det sista filtret samlar endast UDP-trafik.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Följande exempel är den förväntade `az network watcher packet-capture create` utdata från att köra kommandot.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Hämta en paketfångst

Kör `az network watcher packet-capture show-status` kommandot, hämtar status för en som körs eller slutförs paketfångsten.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Följande exempel är utdata `az network watcher packet-capture show-status` från kommandot. Följande exempel är när hämtningen stoppas, med en StopReason of TimeExceed.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Stoppa en paketfångst

Genom att `az network watcher packet-capture stop` köra kommandot, om en insamlingssession pågår stoppas den.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Kommandot returnerar inget svar när det kördes på en hämtningssession som körs eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort en paketfångst

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Om du tar bort en paketfångst tas inte filen bort i lagringskontot.

## <a name="download-a-packet-capture"></a>Ladda ned en paketfångst

När pakethämtningssessionen har slutförts kan hämtningsfilen överföras till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketinsamlingen definieras när sessionen skapas. Ett praktiskt verktyg för att komma åt dessa hämtningsfiler som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som kan laddas ner här:https://storageexplorer.com/

Om ett lagringskonto anges sparas paketinsamlingsfiler i ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar paketinsamlingar med aviseringar för virtuella datorer genom att visa [Skapa en aviseringsutlöst paketfångst](network-watcher-alert-triggered-packet-capture.md)

Ta reda på om viss trafik tillåts i eller ut ur den virtuella datorn genom att besöka [Kontrollera IP-flödeskontroll](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
