---
title: "Hantera paket insamlingar med Nätverksbevakaren Azure - Azure CLI 2.0 | Microsoft Docs"
description: "Den här sidan förklarar hur du hanterar funktionen paket avbildning i Nätverksbevakaren som använder Azure CLI 2.0"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 66f53f97220f8fd23fa38bece0025f8b48289e23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-20"></a>Hantera paket insamlingar med Azure Nätverksbevakaren använder Azure CLI 2.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST-API](network-watcher-packet-capture-manage-rest.md)

Nätverket Watcher paketinsamling kan du skapa avbildning sessioner för att spåra trafik till och från en virtuell dator. Filter har angetts för hämtningens så du fångar upp trafiken som du vill använda. Det hjälper dig för att diagnostisera nätverk avvikelser reaktivt och proaktivt paketinsamling. Andra användningsområden omfattar att samla in nätverksstatistik får information om nätverket intrång felsöka klient-/ serverkommunikation och mycket mer. Genom att via fjärranslutning utlösa paket insamlingar, underlättar den här funktionen för att köra en paketinsamling manuellt och på den önskade datorn, vilket sparar värdefull tid.

Den här artikeln använder våra nästa generations CLI för hantering av resursdistributionsmodell, Azure CLI 2.0, som är tillgänglig för Windows, Mac och Linux.

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

Den här artikeln tar dig igenom de olika administrativa uppgifter som är tillgängliga för paketinsamling.

- [**Starta en paketinsamling**](#start-a-packet-capture)
- [**Stoppa en paketinsamling**](#stop-a-packet-capture)
- [**Ta bort en paketinsamling**](#delete-a-packet-capture)
- [**Hämta en paketinsamling**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

- En instans av Nätverksbevakaren i den region som du vill skapa en paketinsamling
- En virtuell dator med filnamnstillägget paket avbilda aktiverad.

> [!IMPORTANT]
> Paketinsamling kräver en agent körs på den virtuella datorn. Agenten är installerad som ett tillägg. Anvisningar för VM-tillägg, besök [tillägg för virtuell dator och funktioner](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Installera tillägg för virtuell dator

### <a name="step-1"></a>Steg 1

Kör den `az vm extension set` för att installera paketet avbilda agenten på den virtuella gästdatorn.

För Windows-datorer:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

För Linux virtuella datorer:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
````

### <a name="step-2"></a>Steg 2

För att säkerställa att agenten är installerad, kör den `vm extension show` cmdlet och skickar den resursnamnet för grupp och den virtuella datorn. Kontrollera listan för att säkerställa att agenten är installerad.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

I följande exempel är ett exempel på svar från att köras`az vm extension show`

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

## <a name="start-a-packet-capture"></a>Starta en paketinsamling

När de föregående stegen är klar kan är paket avbilda agenten installerad på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Nästa steg är att hämta Nätverksbevakaren-instans. Tdet namnet på Nätverksbevakaren har överförts till den `az network watcher show` cmdlet i steg 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Steg 2

Hämta ett lagringskonto. Det här lagringskontot används för att spara filen i paketet.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Steg 3

Filter kan användas för att begränsa de data som lagras med paketinsamling. I följande exempel ställer in en paketinsamling med flera filter.  De tre första filter samlar in utgående TCP-trafik endast lokala IP 10.0.0.3 till målportar 20, 80 och 443.  Filtret som samlar in UDP-trafik.

```azurecli
az network watcher packet-capture create --resource-group {resoureceurceGroupName} --vm {vmName} --name packetCaptureName --storage-account gwteststorage123abc --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Följande är exempel på utdata som förväntas från att köras i `az network watcher packet-capture create` cmdlet.

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

## <a name="get-a-packet-capture"></a>Hämta en paketinsamling

Kör den `az network watcher packet-capture show` cmdlet, hämtar status för en paketinsamling som körs eller har slutförts.

```azurecli
az network watcher packet-capture show --name packetCaptureName --location westcentralus
```

Följande är exempel på utdata från den `az network watcher packet-capture show` cmdlet. I följande exempel är när avbildningen har slutförts. Värdet för PacketCaptureStatus stoppas med en StopReason TimeExceeded. Det här värdet visar att paketinsamling lyckades och kördes tiden.

```
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
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/providers/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapt
ure_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="stop-a-packet-capture"></a>Stoppa en paketinsamling

Genom att köra den `az network watcher packet-capture stop` cmdlet, om en avbildningssessionen pågår den har stoppats.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Cmdleten returnerar inga svar när kördes på en pågående avbildningssessionen eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort en paketinsamling

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Om du tar bort en paketinsamling tar inte bort filen i lagringskontot.

## <a name="download-a-packet-capture"></a>Hämta en paketinsamling

När paketet avbildningssessionen är klar, kan avbilda filen laddas upp till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketinsamling har definierats vid skapandet av sessionen. Ett enkelt verktyg för att komma åt dessa avbilda filer som sparats till ett lagringskonto är Microsoft Azure Lagringsutforskaren, som kan hämtas här: http://storageexplorer.com/

Om ett storage-konto anges sparas paket avbilda filer till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nästa steg

Lär dig att automatisera insamlingar paket med virtuella aviseringar genom att visa [skapar en avisering utlösta paketinsamling](network-watcher-alert-triggered-packet-capture.md)

Hitta om vissa trafik tillåts i eller utanför den virtuella datorn genom att besöka [Kontrollera Kontrollera IP-flöde](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
