---
title: Hantera infångade paket med Azure Network Watcher – Azure CLI | Microsoft Docs
description: Den här sidan förklarar hur du hanterar paket avbildningsfunktionen i Network Watcher med Azure CLI
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: cf03872607546f38d19a280f65f641abf627268b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726884"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Hantera infångade paket med Azure Network Watcher med Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST-API](network-watcher-packet-capture-manage-rest.md)

Network Watcher-infångade kan du skapa avbildning sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för avbildningssessionen att se till att du fångar upp trafiken som du vill. Det hjälper dig för att diagnostisera nätverk avvikelser både reaktivt och proaktivt infångade paket. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång, felsöka klient-/ serverkommunikation och mycket mer. Genom för att utlösa infångade paket via en fjärranslutning, förenklar med den här funktionen ansvaret för att köra ett infångat manuellt och på den önskade datorn, vilket sparar värdefull tid.

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)](/cli/azure/install-azure-cli).

Den här artikeln tar dig igenom de olika administrativa uppgifter som är tillgängliga för infångade paket.

- [**Starta ett infångat paket**](#start-a-packet-capture)
- [**Stoppa ett infångat paket**](#stop-a-packet-capture)
- [**Ta bort ett infångat paket**](#delete-a-packet-capture)
- [**Ladda ned ett infångat paket**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

- En instans av Network Watcher i regionen som du vill skapa ett infångat paket
- En virtuell dator med packet capture tillägget aktiverat.

> [!IMPORTANT]
> Paketfångsten kräver en agent körs på den virtuella datorn. Agenten installeras som ett tillägg. Anvisningar för VM-tillägg, besök [virtuella datorer, tillägg och funktioner](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Installera VM-tillägg

### <a name="step-1"></a>Steg 1

Kör den `az vm extension set` cmdlet för att installera packet capture agenten på den virtuella gästdatorn.

För Windows-datorer:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

För Linux-datorer:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
```

### <a name="step-2"></a>Steg 2

Om du vill kontrollera att agenten är installerad, kör den `vm extension show` cmdlet och skickar den resursnamnet grupp och den virtuella datorn. Kontrollera den resulterande listan för att säkerställa att agenten är installerad.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

I följande exempel är ett exempel på svaret från att köras `az vm extension show`

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

## <a name="start-a-packet-capture"></a>Starta ett infångat paket

När de föregående stegen har slutförts, är packet capture agenten installerad på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Nästa steg är att hämta Network Watcher-instans. Den här namn i Network Watcher skickas till den `az network watcher show` cmdlet i steg 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Steg 2

Hämta ett storage-konto. Det här lagringskontot används för att lagra filen packet capture.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Steg 3

Filter kan användas för att begränsa de data som lagras med paketfångsten. I följande exempel ställer in ett infångat paket med flera filter.  De första tre filter samlar in utgående TCP-trafik från lokala IP 10.0.0.3 till målportar 20, 80 och 443.  Senaste filtret samlar in UDP-trafik.

```azurecli
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

I följande exempel är utdatan som förväntas från att köras i `az network watcher packet-capture create` cmdlet.

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

## <a name="get-a-packet-capture"></a>Hämta ett infångat paket

Kör den `az network watcher packet-capture show-status` cmdleten hämtar status för ett infångat paket som körs för tillfället, eller slutfördes.

```azurecli
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

I följande exempel är utdata från den `az network watcher packet-capture show-status` cmdlet. I följande exempel är när avbildningen har stoppats, med en StopReason TimeExceeded. 

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

## <a name="stop-a-packet-capture"></a>Stoppa ett infångat paket

Genom att köra den `az network watcher packet-capture stop` cmdlet, om en avbildningssessionen är håller på att den har stoppats.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Cmdleten returnerar inga svar när kördes på en pågående avbildningssessionen eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort ett infångat paket

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Filen i lagringskontot tas inte bort om du tar bort ett infångat paket.

## <a name="download-a-packet-capture"></a>Ladda ned ett infångat paket

När packet capture sessionen är klar kan att överföra filen avbildning till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketfångsten har definierats vid skapandet av sessionen. Ett praktiskt verktyg för att komma åt dessa avbilda filer som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagringskonto anges sparas packet capture filer till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar infångade paket med virtuella datorer aviseringar genom att visa [skapar en avisering utlösta paketfångsten](network-watcher-alert-triggered-packet-capture.md)

Hitta om vissa trafik är tillåten i eller utanför din virtuella dator genom att besöka [Kontrollera Kontrollera IP-flöde](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
