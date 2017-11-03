---
title: Flytta en virtuell Linux-dator i Azure | Microsoft Docs
description: Flytta en Linux VM till en annan Azure-prenumerationen eller resursen i Resource Manager-distributionsmodellen.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 4695a9c934f97f2b2d448c4990e7ad5533e38e9f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Flytta en Linux VM till en annan prenumeration eller resurs
Den här artikeln får du veta hur du flyttar en Linux VM mellan resursgrupper eller prenumerationer. Flytta en virtuell dator mellan prenumerationer kan vara användbar om du har skapat en virtuell dator i en personlig prenumeration och nu vill flytta den till din företagsprenumeration.

> [!IMPORTANT]
>Du kan inte flytta hanterade diskar just nu. 
>
>Ny resurs-ID: N skapas som en del av migreringen. När den virtuella datorn har flyttats, måste du uppdatera verktyg och skript för att använda den nya resursen med ID: N. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Använda Azure CLI för att flytta en virtuell dator
Om du vill för att flytta en virtuell dator, måste du flytta den virtuella datorn och alla dess stödfiler resurser. Använd den **azure-grupp visa** kommando för att visa en lista över alla resurser i en resursgrupp och deras ID. Det hjälper till att skicka utdata från kommandot till en fil så att du kan kopiera och klistra in de ID: N i senare kommandon.

    azure group show <resourceGroupName>

Om du vill flytta en virtuell dator och dess resurser till en annan resursgrupp, Använd den **azure-resurs flytta** CLI-kommando. I följande exempel visas hur du flyttar en virtuell dator och de vanligaste resurser som krävs. Vi använder den **-i** parameter- och pass i en kommaavgränsad lista (utan blanksteg) med ID: N för resurser att flytta.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

Om du vill flytta den virtuella datorn och dess resurser till en annan prenumeration lägger du till den **--mål subscriptionId &#60; destinationSubscriptionID &#62;** målprenumerationen med parametern.

Om du arbetar från Kommandotolken på en Windows-dator kan du behöva lägga till en  **$**  framför variabelnamn när du deklarerar dem. Detta är inte behövs i Linux.

Du uppmanas att bekräfta att du vill flytta den angivna resursen. Typen **Y** att bekräfta att du vill flytta resurserna.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

