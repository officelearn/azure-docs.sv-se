---
title: Skapa en virtuell Windows-dator med ny AzVM i Azure | Microsoft Docs
description: "Azure PowerShell-skript Sample - skapa en virtuell Windows-dator med hjälp av cmdlet New-AzVM."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: cynthn
ms.openlocfilehash: 36660ea1e8609ea8a03f4fa63e6f12f765bf9721
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-configured-virtual-machine-with-the-new-azvm-powershell-cmdlet-preview"></a>Skapa en helt konfigurerade virtuell dator med ny AzVM PowerShell-cmdlet (förhandsgranskning)

Det här skriptet använder cmdleten New-AzVM i molnet Shell för att skapa en Azure-dator som kör Windows Server 2016. När skriptet har körts kan du komma åt den virtuella datorn via RDP. Molnet Shell har modulen som innehåller ny AzVM installeras som standard, vilket gör det lättare för dig att checka ut de nya funktionerna.

Det här exemplet använder cmdleten New-AzVM är en förhandsversion. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="sample-script"></a>Exempelskript


```azurepowershell-interactive
New-AzVM -Name myVM `
    -VirtualNetworkName myVNet `
    -Location westeurope `
    -SecurityGroupName myNSG `
    -PublicIpAddressName myPIP 
```


## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare virtuella PowerShell-skript-exempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
