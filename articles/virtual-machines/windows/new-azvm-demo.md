---
title: Skapa virtuell Windows-dator med hjälp av cmdleten för förenklad New-AzVM i Azure Cloud Shell | Microsoft Docs
description: Lär dig snabbt att skapa Windows-datorer med förenklad cmdleten New-AzVM i Azure Cloud Shell.
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
ms.openlocfilehash: 02f2bd78ca5656534b106c6f7f18c05165b4b9ff
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444506"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Skapa en Windows-dator med förenklad cmdlet New-AzVM i Cloud Shell 

Den [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet har lagt till en förenklad parameteruppsättning för att skapa en ny virtuell dator med hjälp av PowerShell. Det här avsnittet visar hur du använder PowerShell i Azure Cloud Shell, med den senaste versionen av cmdleten New-AzureVM förinstallerad, för att skapa en ny virtuell dator. Vi använder en förenklad parameteruppsättning som automatiskt skapar alla nödvändiga resurser med hjälp av smarta standardinställningar. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan använda cmdleten [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) för att skapa en virtuell dator med smarta standardinställningar som inbegriper användning av Windows Server 2016 Datacenter-avbildning från Azure Marketplace. Du kan använda New-AzVM med bara de **-namnet** parametern och den använder detta värde för alla resursnamnen. I det här exemplet har vi ställt in parametern **-Name** som *myVM*. 

Kontrollera att **PowerShell** är valt i Cloud Shell och skriv:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Du uppmanas att skapa ett användarnamn och lösenord för den virtuella datorn som ska användas när du ansluter till den virtuella datorn senare i det här avsnittet. Lösenordet måste innehålla mellan 12 och 123 tecken och uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken.

Det tar en liten stund att skapa den virtuella datorn och de associerade resurserna. När du är klar kan du se alla resurser som har skapats med den [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet.

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Använd den [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) kommando för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna IP-adressen.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Öppna en kommandotolk på den lokala datorn och använda den **mstsc** kommando för att starta en fjärrskrivbordssession med den nya virtuella datorn. Ersätt &lt;publicIPAddress&gt; med IP-adressen för den virtuella datorn. När du uppmanas, anger du användarnamnet och lösenordet du gav den virtuella datorn när den skapades.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Ange olika namn

Du kan också ge mer beskrivande namn för resurserna och fortfarande har dem skapas automatiskt. Här är ett exempel där vi har gett flera resurser för den nya virtuella datorn, inklusive en ny resursgrupp.

```azurepowershell-interactive
New-AzVm `
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

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du distribuerat en enkel virtuell dator med New-AzVM och sedan anslutit den via RDP. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
