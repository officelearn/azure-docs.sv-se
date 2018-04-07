---
title: Flytta en virtuell Windows-dator-resurs i Azure | Microsoft Docs
description: Flytta en virtuell Windows-dator till en annan Azure-prenumerationen eller resursen i Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytta en virtuell Windows-dator till en annan Azure-prenumerationen eller resursen grupp
Den här artikeln får du veta hur du flyttar en virtuell Windows-dator mellan resursgrupper eller prenumerationer. Flytta mellan prenumerationer kan vara användbar om du ursprungligen har skapat en virtuell dator i en personlig prenumeration och nu vill flytta den till ditt företags prenumeration för att fortsätta arbeta.

> [!IMPORTANT]
>Du kan inte flytta hanterade diskar just nu. 
>
>Ny resurs-ID: N skapas som en del av migreringen. När den virtuella datorn har flyttats, måste du uppdatera verktyg och skript för att använda den nya resursen med ID: N. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Använd Powershell för att flytta en virtuell dator

Om du vill flytta en virtuell dator till en annan resursgrupp, måste du se till att du även flytta alla beroende resurser. Om du vill använda cmdleten Move-AzureRMResource måste ResourceId för varje resurs. Du kan hämta en lista över ResourceId med hjälp av den [hitta AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource) cmdlet.

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Om du vill flytta en virtuell dator som vi behöver flytta flera resurser. Vi kan använda utdata från hitta AzureRMResource för att skapa en kommaavgränsad lista över ResourceIds och skicka som att [flytta AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) flytta dem till målet. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
För att flytta resurser till en annan prenumeration, innehåller den **- DestinationSubscriptionId** parameter. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Du blir ombedd att bekräfta att du vill flytta de angivna resurserna. 

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

