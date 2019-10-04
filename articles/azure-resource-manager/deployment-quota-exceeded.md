---
title: Kvoten för Azure-distributionen överskreds
description: Beskriver hur du löser problemet med fler än 800 distributioner i resurs grupps historiken.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: tomfitz
ms.openlocfilehash: 755383c9d40c104d50ad9bb7a31b3a00f8348313
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827021"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Lös fel när antalet distributioner överskrider 800

Varje resurs grupp är begränsad till 800 distributioner i distributions historiken. I den här artikeln beskrivs det fel som du får när en distribution Miss lyckas eftersom den skulle överskrida de tillåtna 800-distributionerna. Lös problemet genom att ta bort distributioner från resurs grupps historiken. Att ta bort en distribution från historiken påverkar inte några av de distribuerade resurserna.

## <a name="symptom"></a>Symtom

Under distributionen får du ett fel meddelande om att den aktuella distributionen kommer att överskrida kvoten på 800-distributioner.

## <a name="solution"></a>Lösning

### <a name="azure-cli"></a>Azure CLI

Använd kommandot [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) för att ta bort distributioner från historiken.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Du kan hämta det aktuella antalet i distributions historiken med följande kommando:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd kommandot [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) för att ta bort distributioner från historiken.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Du kan hämta det aktuella antalet i distributions historiken med följande kommando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Lösningar från tredje part

Följande externa lösningar riktar sig till vissa scenarier:

* [Azure Logic Apps-och PowerShell-lösningar](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps-tillägg](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
