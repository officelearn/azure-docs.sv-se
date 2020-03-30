---
title: Hantera paketinsamlingar – Azure PowerShell
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du hanterar paketinsamlingsfunktionen i Network Watcher med PowerShell
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
ms.openlocfilehash: 06263f85f7d6ad6cc80724baab01124833498739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129648"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Hantera paketinsamlingar med Azure Network Watcher med PowerShell

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [REST-API för Azure](network-watcher-packet-capture-manage-rest.md)

Med network watcher-paketfånget kan du skapa insamlingssessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingssessionen för att säkerställa att du bara samlar in den trafik du vill ha. Paketinsamling hjälper till att diagnostisera nätverksavvikelser både reaktivt och proaktivt. Andra användningsområden inkluderar att samla in nätverksstatistik, få information om nätverksintrång, att felsöka klient-server kommunikation och mycket mer. Genom att fjärrutlösa paketinfångningar, underlättar den här funktionen bördan av att köra en paketfångst manuellt och på önskad maskin, vilket sparar värdefull tid.

Den här artikeln tar dig igenom de olika hanteringsuppgifter som för närvarande är tillgängliga för paketinsamling.

- [**Starta en paketfångst**](#start-a-packet-capture)
- [**Stoppa en paketfångst**](#stop-a-packet-capture)
- [**Ta bort en paketfångst**](#delete-a-packet-capture)
- [**Ladda ned en paketfångst**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

* En instans av Network Watcher i den region som du vill skapa en paketfångst

* En virtuell dator med paketfångstenstillägget aktiverat.

> [!IMPORTANT]
> Paketfångsten kräver `AzureNetworkWatcherExtension`ett tillägg för den virtuella datorn . För att installera tillägget på en Windows VM besök [Azure Network Watcher Agent virtuell dator tillägg för Windows](../virtual-machines/windows/extensions-nwa.md) och för Linux VM besök Azure Network [Watcher Agent virtuell dator tillägg för Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Installera VM-tillägg

### <a name="step-1"></a>Steg 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Steg 2

I följande exempel hämtar du den `Set-AzVMExtension` tilläggsinformation som behövs för att köra cmdleten. Den här cmdleten installerar paketfångstensagenten på den virtuella gästdatorn.

> [!NOTE]
> Cmdleten `Set-AzVMExtension` kan ta flera minuter att slutföra.

För virtuella Windows-datorer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

För virtuella Linux-datorer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Följande exempel är ett lyckat `Set-AzVMExtension` svar efter att cmdlet har körts.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Steg 3

För att säkerställa att agenten `Get-AzVMExtension` är installerad, kör cmdlet och skicka den den virtuella datorns namn och tilläggets namn.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Följande exempel är ett exempel på svaret från att köra`Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Starta en paketfångst

När föregående steg är klara installeras paketfångningsagenten på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Nästa steg är att hämta Network Watcher-instansen. Denna variabel skickas `New-AzNetworkWatcherPacketCapture` till cmdlet i steg 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Steg 2

Hämta ett lagringskonto. Det här lagringskontot används för att lagra paketinsamlingsfilen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Steg 3

Filter kan användas för att begränsa de data som lagras av paketinsamlingen. I följande exempel ställs in två filter.  Ett filter samlar endast ingående TCP-trafik från lokal IP 10.0.0.3 till målportarna 20, 80 och 443.  Det andra filtret samlar endast UDP-trafik.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Flera filter kan definieras för en paketfångst.

### <a name="step-4"></a>Steg 4

Kör `New-AzNetworkWatcherPacketCapture` cmdleten för att starta paketfångsten och skicka de nödvändiga värdena som hämtats i föregående steg.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Följande exempel är den förväntade `New-AzNetworkWatcherPacketCapture` utgången från att köra cmdleten.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Hämta en paketfångst

Kör `Get-AzNetworkWatcherPacketCapture` cmdlet, hämtar status för en närvarande kör, eller slutförda paketfångsten.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Följande exempel är utdata `Get-AzNetworkWatcherPacketCapture` från cmdleten. Följande exempel är när hämtningen är klar. Värdet PacketCaptureStatus stoppas med en StopReason of TimeExceed. Det här värdet visar att paketinsamlingen lyckades och körde sin tid.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Stoppa en paketfångst

Genom att `Stop-AzNetworkWatcherPacketCapture` köra cmdlet, om en fångstsession pågår det stoppas.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Cmdleten returnerar inget svar när det kördes på en hämtningssession som körs eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort en paketfångst

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














