---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a528fad10144ec733a3db5340ef12dee5ce5411c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553893"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Resurser per [resursgrupp](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), per resurstyp |800 |Varierar per resurstyp |
| Distributioner per resursgrupp i distributionshistoriken |800 |800 |
| Resurser per distribution |800 |800 |
| Hanteringslås per unikt |20 |20 |
| Antal taggar per resurs eller resursgrupp |15 |15 |
| Taggen nyckellängd |512 |512 |
| Taggen värdets längd |256 |256 |


#### <a name="template-limits"></a>Mall för gränser

| Värde | Standardgräns | Övre gräns |
| --- | --- | --- |
| Parametrar |256 |256 |
| Variabler |256 |256 |
| Resurser som innehåller antal kopior |800 |800 |
| Utdata |64 |64 |
| Malluttryck |24,576 tecken |24,576 tecken |
| Resurser i exporterade mallar |200 |200 | 
| Mallstorlek |1 MB |1 MB |
| Parametern filstorlek |64 kB |64 kB |

Du kan överskrida vissa begränsningar för mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från historiken som inte längre behövs. Du kan ta bort poster från historiken över med [az group deployment ta bort](/cli/azure/group/deployment) för Azure CLI. Du kan också använda [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) i PowerShell. Ta bort en post från distributionshistoriken påverkar inte av distributionsresurser. 