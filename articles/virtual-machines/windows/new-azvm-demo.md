---
title: Skapa en virtuell Windows-dator med cmdleten New-AzVM i Azure Cloud Shell| Microsoft Docs
description: "Lär dig att skapa en virtuell Windows-dator snabbt med cmdlet:en New-AzVMcmdlet i Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Skapa en virtuell Windows-dator med New-AzVM (förhandsversion) i Cloud Shell 

Den nya cmdleten New-AzVM (förhandsversion) är ett förenklat sätt att skapa en ny virtuell dator med PowerShell. Den här guiden beskriver hur du använder PowerShell i Azure Cloud Shell med cmdleten New-AzVM förinstallerad, för att skapa en ny virtuell Azure-dator som kör Windows Server 2016. När distributionen är klar kan vi ansluta till servern med RDP.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Du kan använda cmdleten **New-AzVM** för att skapa en virtuell dator med smarta standardinställningar som inbegriper användning av Windows Server 2016 Datacenter-avbildning från Azure Marketplace. Du kan använda New-AzVM självständigt, och då används standardvärden för resursnamnen. I det här exemplet har vi ställt in parametern **-Name** som *myVM*. Cmdleten skapar alla resurser som behövs med *myVM* som prefix för resursnamnet. 

Kontrollera att **PowerShell** är valt i Cloud Shell och skriv:

```azurepowershell-interactive
New-AzVm -Name myVM
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

Öppna en kommandotolk på din lokala dator och använd kommandot **mstsc** för att starta en inloggningssession med din nya virtuella dator. Ersätt &lt;publicIPAddress&gt; med IP-adressen för den virtuella datorn. När du uppmanas, anger du användarnamnet och lösenordet du gav den virtuella datorn när den skapades.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du distribuerat en enkel virtuell dator med New-AzVM och sedan anslutit den via RDP. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
