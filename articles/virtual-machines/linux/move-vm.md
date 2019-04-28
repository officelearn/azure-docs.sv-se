---
title: Flytta en virtuell Linux-dator i Azure | Microsoft Docs
description: Flytta en Linux-VM till en annan Azure-prenumeration eller resurs i Resource Manager-distributionsmodellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: d2d3f36c9b4ee0557f9e060bec762877a94ea637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473947"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Flytta en Linux-VM till en annan prenumeration eller resursgrupp grupp
Den här artikeln vägleder dig genom hur du flyttar en Linux-dator (VM) mellan resursgrupper eller prenumerationer. Flytta en virtuell dator mellan prenumerationer kan vara praktiskt om du har skapat en virtuell dator i en personlig prenumeration och nu vill flytta det till ditt företags prenumeration.

> [!IMPORTANT]
>Du kan inte flytta Azure Managed Disks just nu. 
>
>Ny resurs-ID: N skapas som en del av migreringen. När den virtuella datorn har flyttats, kommer du behöva uppdatera dina verktyg och skript för att använda nya resurs-ID. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Använda Azure CLI för att flytta en virtuell dator


Innan du kan flytta den virtuella datorn med hjälp av Azure CLI, måste du kontrollera att käll- och målprenumerationer finns inom samma klientorganisation. Kontrollera att båda prenumerationerna har samma klient-ID genom att använda [az konto show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Om klient-ID: N för käll- och målprenumerationer inte är samma, måste du kontakta [stöder](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) flytta resurserna till en ny klient.

Om du vill flytta en virtuell dator, måste du flytta den virtuella datorn och alla dess resurser. Använd den [az resurslistan](/cli/azure/resource) kommando för att lista alla resurser i en resursgrupp och deras ID: N. Det hjälper dig att skicka vidare kommandots utdata till en fil så att du kan kopiera och klistra in ID: N i senare kommandon.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Om du vill flytta en virtuell dator och dess resurser till en annan resursgrupp, använda [az resursflytt](/cli/azure/resource). I följande exempel visas hur du flyttar en virtuell dator och de vanligaste resurser som krävs. Använd den **-ID: n** parametern och pass i en kommaavgränsad lista (utan blanksteg) med ID: N för resurserna att flytta.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Om du vill flytta den virtuella datorn och dess resurser till en annan prenumeration, lägger du till den **--mål-subscriptionId** parametern för att ange målprenumerationen.

När du uppmanas att bekräfta att du vill flytta de angivna resurserna genom att ange **Y** att bekräfta.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).    

