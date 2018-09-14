---
title: Flytta en Windows VM-resurs i Azure | Microsoft Docs
description: Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resurs i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 1daf04e3f878d0748bfa0904259c7b7187481843
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580502"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resursgrupp grupp
Den här artikeln vägleder dig genom hur du flyttar en Windows virtuell dator (VM) mellan resursgrupper eller prenumerationer. Flytta mellan prenumerationer kan vara användbart om du skapade en virtuell dator i en personlig prenumeration och nu vill flytta det till ditt företags prenumeration och fortsätt ditt arbete.

> [!IMPORTANT]
>Du kan inte flytta Azure Managed Disks just nu. 
>
>Ny resurs-ID: N skapas som en del av migreringen. När den virtuella datorn har flyttats, kommer du behöva uppdatera dina verktyg och skript för att använda nya resurs-ID. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Använd Powershell för att flytta en virtuell dator

Om du vill flytta en virtuell dator till en annan resursgrupp måste du se till att du också flytta alla beroende resurser. Hämta en lista med resurs-ID för var och en av de här resurserna med den [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) cmdlet.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Du kan använda utdata från föregående kommando som en kommaavgränsad lista med resurs-ID: N till [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) att flytta varje resurs till målet. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
För att flytta resurser till en annan prenumeration, innehåller den **- DestinationSubscriptionId** parametern. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


När du uppmanas att bekräfta att du vill flytta de angivna resurserna genom att ange **Y** att bekräfta.

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

