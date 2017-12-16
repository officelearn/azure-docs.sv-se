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
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
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


Innan du kan flytta den virtuella datorn med hjälp av CLI, måste du kontrollera att käll- och prenumerationer finns i samma klientorganisation. Kontrollera att båda prenumerationer har samma klient-ID genom att använda [az konto visa](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Om klient-ID: N för käll- och -prenumerationer inte är samma, måste du kontakta [stöder](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) att flytta resurser till en ny klient.

Om du vill för att flytta en virtuell dator, måste du flytta den virtuella datorn och alla dess stödfiler resurser. Använd den [az resurslistan](/cli/azure/resource#az_resource_list) kommando för att visa en lista över alla resurser i en resursgrupp och deras ID. Det hjälper till att skicka utdata från kommandot till en fil så att du kan kopiera och klistra in de ID: N i senare kommandon.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Om du vill flytta en virtuell dator och dess resurser till en annan resursgrupp, Använd [az resursflyttningen](/cli/azure/resource#az_resource_move). I följande exempel visas hur du flyttar en virtuell dator och de vanligaste resurser som krävs. Använd den **-ID: n** parameter- och pass i en kommaavgränsad lista (utan blanksteg) med ID: N för resurser att flytta.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Om du vill flytta den virtuella datorn och dess resurser till en annan prenumeration lägger du till den **--mål subscriptionId** målprenumerationen med parametern.

Om du uppmanas att bekräfta att du vill flytta den angivna resursen. Typen **Y** att bekräfta att du vill flytta resurserna.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

