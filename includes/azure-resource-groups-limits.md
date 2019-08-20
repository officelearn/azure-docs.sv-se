---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 25928ef35da1ce4b3824303a5d46749c32aa701f
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626408"
---
| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| Resurser per [resurs grupp](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), per resurs typ |800 |Vissa resurs typer kan överskrida gränsen på 800. Se [resurser som inte är begränsade till 800 instanser per resurs grupp](../articles/azure-resource-manager/resources-without-rg-limit.md). |
| Distributioner per resurs grupp i distributions historiken |800<sup>1</sup> |800 |
| Resurser per distribution |800 |800 |
| Hanterings lås per unik omfattning |20 |20 |
| Antal Taggar per resurs eller resurs grupp |50 |50 |
| Etikett nyckel längd |512 |512 |
| Tagg värde längd |256 |256 |

<sup>1</sup> Om du når gränsen på 800-distributioner per resurs grupp tar du bort distributioner från den historik som inte längre behövs. Om du tar bort en post från distributions historiken påverkas inte de distribuerade resurserna. Du kan ta bort poster från historiken med [AZ Group Deployment Delete](/cli/azure/group/deployment) för Azure CLI eller [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) i PowerShell.  Ett PowerShell-skript som automatiserar borttagning av distributioner i ett scenario med kontinuerlig integrering och kontinuerlig leverans (CI/CD) finns i [Remove-Deployments. ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Mall gränser

| Value | Standardgräns | Övre gräns |
| --- | --- | --- |
| Parametrar |256 |256 |
| Variabler |256 |256 |
| Resurser (inklusive antal exemplar) |800 |800 |
| outputs |64 |64 |
| Mall uttryck |24 576 tecken |24 576 tecken |
| Resurser i exporterade mallar |200 |200 | 
| Mallens storlek |4 MB |4 MB |
| Parameter fil storlek |64 kB |64 kB |

Du kan överskrida vissa begränsningar för mallar genom att använda en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/resource-group-linked-templates.md). Om du vill minska antalet parametrar, variabler eller utdata kan du kombinera flera värden i ett objekt. Mer information finns i [objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
