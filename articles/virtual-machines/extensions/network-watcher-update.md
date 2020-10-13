---
title: Uppdatera Network Watcher-tillägget till den senaste versionen
description: Lär dig hur du uppdaterar Network Watcher tillägget till den senaste versionen
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: fd3fff2d438bbf804e35f04db0cfae15eea5e782
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973347"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Så här uppdaterar du Network Watcher-tillägget till den senaste versionen 

## <a name="overview"></a>Översikt

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en övervaknings-, diagnostik-och analys tjänst för nätverks prestanda som gör det möjligt att övervaka Azure-nätverk. Tillägget Network Watcher agent virtuell dator måste vara ett krav för att fånga in nätverks trafik på begäran och andra avancerade funktioner på virtuella Azure-datorer. Network Watcher-tillägget används av funktioner som anslutnings övervakaren, anslutnings övervakaren (för hands version), fel sökning av anslutningar och paket fångst.   

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du har Network Watcher tillägget installerat på den virtuella datorn och ger instruktioner för att uppdatera det till den senaste versionen. 

## <a name="latest-version"></a>Senaste version
Den senaste versionen av Network Watcher-tillägget är för närvarande `1.4.1654.1` .

## <a name="updating-your-extension"></a>Uppdatera tillägget 

### <a name="check-your-extension-version"></a>Kontrol lera tilläggs versionen  

**Använda Azure Portal**

1. Gå till bladet "Extensions" på den virtuella datorn i Azure Portal.   
2. Klicka på "AzureNetworkWatcher"-tillägget för att se informations fönstret.  
3. Leta upp versions numret i fältet ' version '.  

**Använda Azure CLI** Kör kommandot nedan från en Azure CLI-kommandotolk.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Leta upp AzureNetworkWatcher-tillägget i utdata och identifiera versions numret från fältet "TypeHandlerVersion" i utdata.  


**Använda PowerShell** Kör följande kommandon från en PowerShell-prompt:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Leta upp AzureNetworkWatcher-tillägget i utdata och identifiera versions numret från fältet "TypeHandlerVersion" i utdata.   


### <a name="update-your-extension"></a>Uppdatera tillägget

Om din version till exempel är lägre än `1.4.1654.1` (den aktuella senaste versionen) uppdaterar du tillägget med något av följande alternativ. 

**Alternativ 1: Använd PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Alternativ 2: Använd Azure CLI**  

Framtvinga uppgradering 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Om det inte fungerar. Ta bort och installera tillägget igen med hjälp av stegen nedan. Den senaste versionen läggs till automatiskt. 

Tar bort tillägget 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installerar tillägget igen

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Alternativ 3: starta om dina virtuella datorer**

Om du har angett automatisk uppgradering till true för NetworkWatcher-tillägget. Starta om den virtuella datorn installera det senaste tillägget.


## <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du läsa Network Watcher Extension-dokumentationen ([Linux](./network-watcher-linux.md), [Windows](./network-watcher-windows.md)) eller kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).