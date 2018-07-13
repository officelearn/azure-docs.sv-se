---
title: Skapa Windows virtuell dator med förenklad New-AzureRMVM-cmdlet i Azure Cloud Shell | Microsoft Docs
description: Lär dig snabbt att skapa Windows-datorer med förenklad cmdleten New-AzureRMVM i Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: ede8fab67c04eb7ce8d26280de2d1563b6cc8ad2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38678748"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Skapa en Windows-dator med förenklad cmdlet New-AzureRMVM i Cloud Shell 

Den [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) cmdlet har lagt till en förenklad parameteruppsättning för att skapa en ny virtuell dator med hjälp av PowerShell. Det här avsnittet visar hur du använder PowerShell i Azure Cloud Shell, med den senaste versionen av cmdleten New-AzureVM förinstallerad, för att skapa en ny virtuell dator. Vi använder en förenklad parameteruppsättning som automatiskt skapar alla nödvändiga resurser med hjälp av smarta standardinställningar. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 5.1.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan använda den [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) cmdlet för att skapa en virtuell dator med smarta standardinställningar som inbegriper med hjälp av Windows Server 2016 Datacenter-avbildning från Azure Marketplace. Du kan använda New-AzureRMVM med bara de **-namnet** parametern och den använder detta värde för alla resursnamnen. I det här exemplet har vi ställt in parametern **-Name** som *myVM*. 

Kontrollera att **PowerShell** är valt i Cloud Shell och skriv:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Du uppmanas att skapa ett användarnamn och lösenord för den virtuella datorn som ska användas när du ansluter till den virtuella datorn senare i det här avsnittet. Lösenordet måste innehålla mellan 12 och 123 tecken och uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken.

Det tar en liten stund att skapa den virtuella datorn och de associerade resurserna. När du är klar kan du se alla resurser som har skapats med den [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) cmdlet.

```azurepowershell-interactive
Get-AzureRmResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Använd kommandot [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna IP-adressen.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Öppna en kommandotolk på den lokala datorn och använda den **mstsc** kommando för att starta en fjärrskrivbordssession med den nya virtuella datorn. Ersätt &lt;publicIPAddress&gt; med IP-adressen för den virtuella datorn. När du uppmanas, anger du användarnamnet och lösenordet du gav den virtuella datorn när den skapades.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Ange olika namn

Du kan också ge mer beskrivande namn för resurserna och fortfarande har dem skapas automatiskt. Här är ett exempel där vi har gett flera resurser för den nya virtuella datorn, inklusive en ny resursgrupp.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du distribuerat en enkel virtuell dator med New-AzVM och sedan anslutit den via RDP. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
