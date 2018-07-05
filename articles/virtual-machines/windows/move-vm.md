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
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: 168ba57399b2649af29820f7321dd0151618346e
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436488"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resursgrupp grupp
Den här artikeln vägleder dig genom hur du flyttar en virtuell Windows-dator mellan resursgrupper eller prenumerationer. Flytta mellan prenumerationer kan vara användbart om du skapade en virtuell dator i en personlig prenumeration och nu vill flytta det till ditt företags prenumeration och fortsätt ditt arbete.

> [!IMPORTANT]
>Du kan inte flytta Managed Disks just nu. 
>
>Ny resurs-ID: N skapas som en del av migreringen. När den virtuella datorn har flyttats, måste du uppdatera dina verktyg och skript för att använda nya resurs-ID. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Använd Powershell för att flytta en virtuell dator

Om du vill flytta en virtuell dator till en annan resursgrupp måste du se till att du också flytta alla beroende resurser. För att använda cmdleten Move-AzureRMResource, behöver du ResourceId av varje resurs. Du kan hämta en lista över ResourceId med hjälp av den [Get-AzureRMResource](/powershell/module/azurerm.resources/get-azurermresource) cmdlet.

```azurepowershell-interactive
 Get-AzureRMResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Om du vill flytta en virtuell dator som vi behöver flytta flera resurser. Vi kan använda utdata från Get-AzureRMResource för att skapa en kommaavgränsad lista med ResourceIds och skicka den till [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) att flytta dem till målet. 

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


Du blir ombedd att bekräfta att du vill flytta de angivna resurserna. 

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

