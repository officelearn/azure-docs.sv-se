---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3dc09de6afaddeb06b0243eb46e888b673109545
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505939"
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

Om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från historiken som inte längre behövs. Du kan ta bort poster från historiken över med [az group deployment ta bort](/cli/azure/group/deployment) för Azure CLI. Du kan också använda [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) i PowerShell. Ta bort en post från distributionshistoriken påverkar inte av distributionsresurser. 