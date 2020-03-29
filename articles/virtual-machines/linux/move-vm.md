---
title: Flytta en virtuell dator med Azure CLI
description: Flytta en virtuell dator till en annan Azure-prenumeration eller resursgrupp med Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ebcd5f166fd1876f67121787c23d23860c9fa4b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944588"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Flytta en virtuell dator till en annan prenumeration eller resursgrupp
I den här artikeln får du lära dig hur du flyttar en virtuell dator (VM) mellan resursgrupper eller prenumerationer. Det kan vara praktiskt att flytta en virtuell dator mellan prenumerationer om du har skapat en virtuell dator i en personlig prenumeration och nu vill flytta den till företagets prenumeration.

> [!IMPORTANT]
>Nya resurs-ID:er skapas som en del av flytten. När den virtuella datorn har flyttats måste du uppdatera dina verktyg och skript för att använda de nya resurs-ID:na.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Använda Azure CLI för att flytta en virtuell dator


Innan du kan flytta den virtuella datorn med hjälp av Azure CLI måste du se till att käll- och målprenumerationerna finns i samma klientorganisation. Om du vill kontrollera att båda prenumerationerna har samma klient-ID använder du [az-kontoprogram](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Om klient-ID:erna för käll- och målprenumerationerna inte är desamma måste du kontakta [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) för att flytta resurserna till en ny klient.

För att kunna flytta en virtuell dator måste du flytta den virtuella datorn och alla dess stödresurser. Använd kommandot [az resource list](/cli/azure/resource) om du vill visa alla resurser i en resursgrupp och deras ID:er. Det hjälper till att leda utdata från det här kommandot till en fil så att du kan kopiera och klistra in ID:erna i senare kommandon.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Om du vill flytta en virtuell dator och dess resurser till en annan resursgrupp använder du [az-resursflytt](/cli/azure/resource). Följande exempel visar hur du flyttar en virtuell dator och de vanligaste resurserna som krävs. Använd parametern **-ids** och skicka i en kommaavgränsad lista (utan blanksteg) med ID:er som resurserna ska flytta.

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

Om du vill flytta den virtuella datorn och dess resurser till en annan prenumeration lägger du till parametern **--destination-subscriptionId** för att ange målprenumerationen.

När du uppmanas att bekräfta att du vill flytta de angivna resurserna anger du **Y** som ska bekräftas.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
