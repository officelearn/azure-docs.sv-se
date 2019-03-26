---
title: Hantera Azure-resurser med hjälp av Azure CLI | Microsoft Docs
description: Använda Azure CLI och Azure Resource Manager för att hantera dina resurser.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 076c57f5415a4f6f19252fb5a3546e5e9a8a23f4
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417793"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Hantera Azure-resurser med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](resource-group-overview.md) att hantera dina Azure-resurser. För att hantera resursgrupper, se [hantera Azure-resursgrupper med hjälp av Azure CLI](./manage-resource-groups-cli.md).

Andra artiklar om hur du hanterar resurser:

- [Hantera Azure-resurser med hjälp av Azure portal](./manage-resources-portal.md)
- [Hantera Azure-resurser med hjälp av Azure PowerShell](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Du kan distribuera Azure-resurser direkt med hjälp av Azure PowerShell eller distribuera en Resource Manager-mall för att skapa Azure-resurser.

### <a name="deploy-a-resource"></a>Distribution av en resurs

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

Följande skript skapar distribuera en snabbstartsmall för att skapa ett lagringskonto. Mer information finns i [ Snabbstart: Skapa Azure Resource Manager-mallar med hjälp av Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser i gruppen. Mer information finns i [skapa resursgrupp och distribuera resurser](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuera resurser till flera prenumerationer eller resursgrupper

Normalt kan distribuera du alla resurser i mallen till en enda resursgrupp. Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resursgrupper eller prenumerationer. Mer information finns i [distribuera Azure-resurser till flera prenumerationer eller resursgrupper](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Ta bort resurser

Följande skript visar hur du tar bort ett lagringskonto.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Mer information om hur Azure Resource Manager beställningar borttagningen av resurser finns i [borttagning av Azure Resource Manager resource](./resource-group-delete.md).

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

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

## <a name="lock-resources"></a>Lås resurser

Låsning förhindrar andra användare i din organisation av misstag tar bort eller ändrar kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser ett storage-konto så att kontot inte kan tas bort.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Följande skript hämtar alla Lås för ett lagringskonto:

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

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggar hjälper dig att organisera din resursgrupp och resurser logiskt. Mer information finns i [med taggar för att organisera Azure-resurser](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [hantera åtkomst med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).
