---
title: "Skapa Windows virtuell dator med förenklad AzureRMVM ny cmdlet i Azure Cloud Shell | Microsoft Docs"
description: "Lär dig snabbt skapa virtuella Windows-datorer med förenklad AzureRMVM ny cmdlet i Azure Cloud-gränssnittet."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Skapa en Windows-dator med förenklad AzureRMVM ny cmdlet i molnet Shell 

Den [ny AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) cmdlet har lagt till en förenklad uppsättning parametrar för att skapa en ny virtuell dator med hjälp av PowerShell. Det här avsnittet visar hur du använder PowerShell Shell för Azure-molnet, med den senaste versionen av cmdlet New-AzureVM förinstallerat, om du vill skapa en ny virtuell dator. Vi använder en förenklad parameteruppsättning som skapas automatiskt alla nödvändiga resurser med smart standardinställningar. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kursen krävs av Azure PowerShell Modulversion 5.1.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan använda den [ny AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) för att skapa en virtuell dator med smart standardinställningar med hjälp av Windows Server 2016 Datacenter-avbildning från Azure Marketplace. Du kan använda New-AzureRMVM med bara de **-namnet** parametern och den använder värdet för alla resursnamnen. I det här exemplet har vi ställt in parametern **-Name** som *myVM*. 

Kontrollera att **PowerShell** är valt i Cloud Shell och skriv:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Du uppmanas att skapa ett användarnamn och lösenord för den virtuella datorn som ska användas när du ansluter till den virtuella datorn senare i det här avsnittet. Lösenordet måste innehålla mellan 12 och 123 tecken och uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken.

Det tar en liten stund att skapa den virtuella datorn och de associerade resurserna. När det är klart kan du se alla resurser som skapades med cmdlet:en [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Använd kommandot [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna IP-adressen.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Öppna en kommandotolk på din lokala dator och använda den **mstsc** kommando för att starta en fjärrskrivbordssession med den nya virtuella datorn. Ersätt &lt;publicIPAddress&gt; med IP-adressen för den virtuella datorn. När du uppmanas, anger du användarnamnet och lösenordet du gav den virtuella datorn när den skapades.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Ange olika namn

Du kan också ge mer beskrivande namn för resurser och ändå låta dem automatiskt. Här är ett exempel där vi har namnet flera resurser för den nya virtuella datorn, inklusive en ny resursgrupp.

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
