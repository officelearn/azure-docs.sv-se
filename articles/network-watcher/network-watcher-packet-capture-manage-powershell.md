---
title: Hantera infångade paket med Azure Network Watcher – PowerShell | Microsoft Docs
description: Den här sidan förklarar hur du hanterar paket avbildningsfunktionen i Network Watcher med hjälp av PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 267b2c375ef9672c8e5bd7cb8280b4dd40dbcd0d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045551"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Hantera infångade paket med Azure Network Watcher med hjälp av PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST-API](network-watcher-packet-capture-manage-rest.md)

Network Watcher-infångade kan du skapa avbildning sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för avbildningssessionen att se till att du fångar upp trafiken som du vill. Det hjälper dig för att diagnostisera nätverk avvikelser både reaktivt och proaktivt infångade paket. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång, felsöka klient-/ serverkommunikation och mycket mer. Genom för att utlösa infångade paket via en fjärranslutning, förenklar med den här funktionen ansvaret för att köra ett infångat manuellt och på den önskade datorn, vilket sparar värdefull tid.

Den här artikeln tar dig igenom de olika administrativa uppgifter som är tillgängliga för infångade paket.

- [**Starta ett infångat paket**](#start-a-packet-capture)
- [**Stoppa ett infångat paket**](#stop-a-packet-capture)
- [**Ta bort ett infångat paket**](#delete-a-packet-capture)
- [**Ladda ned ett infångat paket**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

* En instans av Network Watcher i regionen som du vill skapa ett infångat paket

* En virtuell dator med packet capture tillägget aktiverat.

> [!IMPORTANT]
> Paketfångsten kräver tillägg för virtuell dator `AzureNetworkWatcherExtension`. Installera tillägget på en Windows-VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Windows](../virtual-machines/windows/extensions-nwa.md) och Linux VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Installera VM-tillägg

### <a name="step-1"></a>Steg 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Steg 2

I följande exempel hämtar tilläggsinformation som behövs för att köra den `Set-AzVMExtension` cmdlet. Denna cmdlet installerar packet capture-agent på den virtuella gästdatorn.

> [!NOTE]
> Den `Set-AzVMExtension` cmdlet kan ta flera minuter att slutföra.

För Windows-datorer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

För Linux-datorer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

I följande exempel är ett lyckat svar efter körning i `Set-AzVMExtension` cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Steg 3

Om du vill kontrollera att agenten är installerad, kör den `Get-AzVMExtension` cmdlet och skicka det virtuella datornamn och namn på tillägg.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

I följande exempel är ett exempel på svaret från att köras `Get-AzVMExtension`

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

## <a name="start-a-packet-capture"></a>Starta ett infångat paket

När de föregående stegen har slutförts, är packet capture agenten installerad på den virtuella datorn.

### <a name="step-1"></a>Steg 1

Nästa steg är att hämta Network Watcher-instans. Den här variabeln skickas till den `New-AzNetworkWatcherPacketCapture` cmdlet i steg 4.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Steg 2

Hämta ett storage-konto. Det här lagringskontot används för att lagra filen packet capture.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Steg 3

Filter kan användas för att begränsa de data som lagras med paketfångsten. I följande exempel ställer in två filter.  Ett filter samlar in utgående TCP-trafik från lokala IP 10.0.0.3 för målportar 20, 80 och 443.  Det andra filtret samlar in UDP-trafik.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Flera filter kan definieras för ett infångat paket.

### <a name="step-4"></a>Steg 4

Kör den `New-AzNetworkWatcherPacketCapture` att starta insamlingen paket skicka värdena som krävs som du hämtade i föregående steg.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

I följande exempel är utdatan som förväntas från att köras i `New-AzNetworkWatcherPacketCapture` cmdlet.

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

## <a name="get-a-packet-capture"></a>Hämta ett infångat paket

Kör den `Get-AzNetworkWatcherPacketCapture` cmdleten hämtar status för ett infångat paket som körs för tillfället, eller slutfördes.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

I följande exempel är utdata från den `Get-AzNetworkWatcherPacketCapture` cmdlet. I följande exempel är när insamlingen är klar. Värdet för PacketCaptureStatus har stoppats, med en StopReason TimeExceeded. Det här värdet visar att paketfångsten lyckades och körde tiden.
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

## <a name="stop-a-packet-capture"></a>Stoppa ett infångat paket

Genom att köra den `Stop-AzNetworkWatcherPacketCapture` cmdlet, om en avbildningssessionen är håller på att den har stoppats.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Cmdleten returnerar inga svar när kördes på en pågående avbildningssessionen eller en befintlig session som redan har stoppats.

## <a name="delete-a-packet-capture"></a>Ta bort ett infångat paket

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














