---
title: Flytta en virtuell Windows-dator-resurs i Azure | Microsoft Docs
description: Flytta en virtuell Windows-dator till en annan Azure-prenumerationen eller resursen i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 1db25a5d9ff5cb6aa2787a0cafa40cfb010e3b06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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
Om du vill flytta en virtuell dator till en annan resursgrupp, måste du se till att du även flytta alla beroende resurser. Om du vill använda cmdleten Move-AzureRMResource, behöver du resursnamnet och typ av resurs. Du kan få båda från Sök AzureRMResource cmdlet.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"


Om du vill flytta en virtuell dator som vi behöver flytta flera resurser. Vi kan bara skapa separat för varje resurs och visa dem. Det här exemplet innehåller de flesta av de grundläggande resurserna för en virtuell dator, men du kan lägga till fler efter behov.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"

    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"

    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

För att flytta resurser till en annan prenumeration, innehåller den **- DestinationSubscriptionId** parameter. 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Du blir ombedd att bekräfta att du vill flytta de angivna resurserna. Typen **Y** att bekräfta att du vill flytta resurserna.

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

