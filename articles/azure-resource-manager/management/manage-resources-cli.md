---
title: Hantera resurser - Azure CLI
description: Använd Azure CLI och Azure Resource Manager för att hantera dina resurser. Visar hur du distribuerar och tar bort resurser.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485538"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Hantera Azure-resurser med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](overview.md) för att hantera dina Azure-resurser. Information om hantering av resursgrupper finns i [Hantera Azure-resursgrupper med hjälp av Azure CLI](manage-resource-groups-cli.md).

Andra artiklar om hantering av resurser:

- [Hantera Azure-resurser med hjälp av Azure-portalen](manage-resources-portal.md)
- [Hantera Azure-resurser med hjälp av Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Du kan distribuera Azure-resurser direkt med hjälp av Azure CLI eller distribuera en Resource Manager-mall för att skapa Azure-resurser.

### <a name="deploy-a-resource"></a>Distribuera en resurs

Följande skript skapar ett lagringskonto.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Distribuera en mall

Följande skript skapar distribuera en Snabbstartsmall för att skapa ett lagringskonto. Mer information finns i [Snabbstart: Skapa Azure Resource Manager-mallar med hjälp av Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser till gruppen. Mer information finns i [Skapa resursgrupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuera resurser till flera prenumerationer eller resursgrupper

Vanligtvis distribuerar du alla resurser i mallen till en enda resursgrupp. Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Mer information finns i [Distribuera Azure-resurser till flera prenumerationer eller resursgrupper](../templates/cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Ta bort resurser

Följande skript visar hur du tar bort ett lagringskonto.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Mer information om hur Azure Resource Manager beställer borttagning av resurser finns i [Azure Resource Manager-resursgruppborttagning](delete-resource-group.md).

## <a name="move-resources"></a>Flytta resurser

Följande skript visar hur du tar bort ett lagringskonto från en resursgrupp till en annan resursgrupp.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Lås resurser

Låsning hindrar andra användare i organisationen från att oavsiktligt ta bort eller ändra kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser ett lagringskonto så att kontot inte kan tas bort.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Följande skript hämtar alla lås för ett lagringskonto:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Följande skript tar bort ett lås för ett lagringskonto:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggning hjälper till att organisera resursgruppen och resurserna logiskt. Information finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) är det sätt som du hanterar åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](overview.md).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../templates/template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).
