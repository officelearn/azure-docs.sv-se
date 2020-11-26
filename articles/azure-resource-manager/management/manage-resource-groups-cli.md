---
title: Hantera resurs grupper – Azure CLI
description: Använd Azure CLI för att hantera dina resurs grupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resurs grupper.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a9a4ed4ebba7f6f2470bb9e7000a899ebc26323
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185818"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Hantera Azure Resource Manager resurs grupper med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](overview.md) för att hantera Azures resurs grupper. Information om hur du hanterar Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure CLI](manage-resources-cli.md).

Andra artiklar om att hantera resurs grupper:

- [Hantera Azure-resurs grupper med hjälp av Azure Portal](manage-resources-portal.md)
- [Hantera Azure-resurs grupper med hjälp av Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Vad är en resurs grupp?

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg vanligt vis till resurser som delar samma livs cykel i samma resurs grupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att dina data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resurs gruppen anger du var metadata lagras.

## <a name="create-resource-groups"></a>Skapa resurs grupper

Följande CLI-kommando skapar en resurs grupp.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Lista resurs grupper

Följande CLI-skript listar resurs grupperna under din prenumeration.

```azurecli-interactive
az group list
```

Så här hämtar du en resurs grupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

Följande CLI-skript tar bort en resurs grupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Mer information om hur Azure Resource Manager beställer borttagningen av resurser finns i [Azure Resource Manager ta bort resurs grupp](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resurs grupp

Se [distribuera resurser till en befintlig resurs grupp](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resurs grupp och resurser

Du kan skapa en resurs grupp och distribuera resurser till gruppen med hjälp av en Resource Manager-mall. Mer information finns i [skapa resurs grupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen Miss lyckas

Den här funktionen kallas även *återgång vid fel*. Mer information finns i [omdistribuera när distributionen Miss lyckas](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resurs grupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resurs grupp. Mer information finns i [Flytta resurser](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Lås resurs grupper

Låsning förhindrar att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser, t. ex. Azure-prenumeration, resurs grupp eller resurs. 

Följande skript låser en resurs grupp så att det inte går att ta bort resurs gruppen.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Följande skript hämtar alla Lås för en resurs grupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Följande skript tar bort ett lås:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Tagga resurs grupper

Du kan använda taggar för resurs grupper och resurser för att logiskt organisera dina till gångar. Mer information finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportera resurs grupper till mallar

När du har konfigurerat resurs gruppen kanske du vill visa Resource Manager-mallen för resurs gruppen. Att exportera mallen erbjuder två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller all fullständig infrastruktur.
- Lär dig mer om mallens syntax genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skriptet visar mallen i-konsolen.  Kopiera JSON och Spara som en fil.

Funktionen Exportera mall stöder inte export av Azure Data Factory-resurser. Information om hur du kan exportera Data Factory-resurser finns i [Kopiera eller klona en data fabrik i Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Om du vill exportera resurser som skapats via den klassiska distributions modellen måste du [migrera dem till distributions modellen för Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Mer information finns i avsnittet [Exportera en och flera resurser till mallen i Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resurs grupper

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) är hur du hanterar åtkomst till resurser i Azure. Mer information finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Mer information Azure Resource Manager finns [Azure Resource Manager översikt](overview.md).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](../templates/template-syntax.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](../index.yml).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).