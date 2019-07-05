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
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: d67f9e9ad99ccd6267de9dbb9fd0c64adb41f0cb
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565564"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resursgrupp grupp
Den här artikeln vägleder dig genom hur du flyttar en Windows virtuell dator (VM) mellan resursgrupper eller prenumerationer. Flytta mellan prenumerationer kan vara användbart om du skapade en virtuell dator i en personlig prenumeration och nu vill flytta det till ditt företags prenumeration och fortsätt ditt arbete. Du behöver inte starta den virtuella datorn för att flytta den och det bör fortsätta att köras under förflyttningen.

> [!IMPORTANT]
>Ny resurs-ID: N skapas som en del av migreringen. När den virtuella datorn har flyttats, kommer du behöva uppdatera dina verktyg och skript för att använda nya resurs-ID. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Använd Powershell för att flytta en virtuell dator

Om du vill flytta en virtuell dator till en annan resursgrupp måste du se till att du också flytta alla beroende resurser. Hämta en lista med resurs-ID för var och en av de här resurserna med den [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

Du kan använda utdata från föregående kommando som en kommaavgränsad lista med resurs-ID: N till [flytta AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) att flytta varje resurs till målet. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
För att flytta resurser till en annan prenumeration, innehåller den **- DestinationSubscriptionId** parametern. 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


När du uppmanas att bekräfta att du vill flytta de angivna resurserna genom att ange **Y** att bekräfta.

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

