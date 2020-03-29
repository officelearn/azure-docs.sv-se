---
title: Flytta en Windows VM-resurs i Azure
description: Flytta en Windows-virtuell dator till en annan Azure-prenumeration eller resursgrupp i Resurshanterarens distributionsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919574"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytta en virtuell Windows-dator till en annan Azure-prenumeration eller resursgrupp
I den här artikeln får du lära dig hur du flyttar en virtuell Dator (VM) mellan resursgrupper eller prenumerationer. Det kan vara praktiskt att flytta mellan prenumerationer om du ursprungligen skapade en virtuell dator i en personlig prenumeration och nu vill flytta den till företagets prenumeration för att fortsätta ditt arbete. Du behöver inte starta den virtuella datorn för att flytta den och den bör fortsätta att köras under flytten.

> [!IMPORTANT]
>Nya resurs-ID:er skapas som en del av flytten. När den virtuella datorn har flyttats måste du uppdatera dina verktyg och skript för att använda de nya resurs-ID:na.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Använda Powershell för att flytta en virtuell dator

Om du vill flytta en virtuell dator till en annan resursgrupp måste du se till att du också flyttar alla beroende resurser. Om du vill hämta en lista med resurs-ID för var och en av dessa resurser använder du cmdleten [Get-AzResource.](https://docs.microsoft.com/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Du kan använda utdata från föregående kommando för att skapa en kommaavgränsad lista över resurs-ID:er till [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) för att flytta varje resurs till målet.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Om du vill flytta resurserna till en annan prenumeration inkluderar du parametern **-DestinationSubscriptionId.**

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


När du uppmanas att bekräfta att du vill flytta de angivna resurserna anger du **Y** som ska bekräftas.

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
