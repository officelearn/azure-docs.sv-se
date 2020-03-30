---
title: Hantera resursgrupper - Azure CLI
description: Använd Azure CLI för att hantera dina resursgrupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resursgrupper.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248338"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Hantera Azure Resource Manager-resursgrupper med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](overview.md) för att hantera dina Azure-resursgrupper. Information om hantering av Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure CLI](manage-resources-cli.md).

Andra artiklar om hantering av resursgrupper:

- [Hantera Azure-resursgrupper med hjälp av Azure-portalen](manage-resources-portal.md)
- [Hantera Azure-resursgrupper med hjälp av Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Vad är en resursgrupp

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg i allmänhet till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du var metadata lagras.

## <a name="create-resource-groups"></a>Skapa resursgrupper

Följande CLI-skript skapar en resursgrupp och visar sedan resursgruppen.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Lista resursgrupper

I följande CLI-skript visas resursgrupperna under din prenumeration.

```azurecli-interactive
az group list
```

Så här hämtar du en resursgrupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

Följande CLI-skript tar bort en resursgrupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Mer information om hur Azure Resource Manager beställer borttagning av resurser finns i [Azure Resource Manager-resursgruppborttagning](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Se [Distribuera resurser till en befintlig resursgrupp](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser till gruppen med hjälp av en Resource Manager-mall. Mer information finns i [Skapa resursgrupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *Återställning vid fel*. Mer information finns i [Omfördela när distributionen misslyckas](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resursgrupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resursgrupp. Mer information finns i [Flytta resurser](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Låsa resursgrupper

Låsning hindrar andra användare i organisationen från att oavsiktligt ta bort eller ändra kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser en resursgrupp så att resursgruppen inte kan tas bort.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Följande skript hämtar alla lås för en resursgrupp:

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

## <a name="tag-resource-groups"></a>Tagga resursgrupper

Du kan använda taggar på resursgrupper och resurser för att logiskt ordna dina resurser. Information finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportera resursgrupper till mallar

När du har konfigurerat resursgruppen kan du visa resurshanterarens mall för resursgruppen. Att exportera mallen ger två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller hela infrastrukturen.
- Lär dig mallsyntaxen genom att titta på Den JavaScript-objekt notation (JSON) som representerar din lösning.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skriptet visar mallen på konsolen.  Kopiera JSON och spara som en fil.

Funktionen för exportmall stöder inte export av Azure Data Factory-resurser. Mer information om hur du kan exportera datafabriksresurser finns i [Kopiera eller klona en datafabrik i Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Om du vill exportera resurser som skapats via den klassiska distributionsmodellen måste du [migrera dem till resurshanterarens distributionsmodell](https://aka.ms/migrateclassicresourcetoarm).

Mer information finns i [Enkel export och export med flera resurser till mallen i Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resursgrupper

[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) är det sätt som du hanterar åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](overview.md).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../templates/template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).