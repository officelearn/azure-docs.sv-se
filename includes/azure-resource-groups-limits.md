---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012531"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Resurser per [resursgrupp](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), per resurstyp |800 |Varierar per resurstyp |
| Distributioner per resursgrupp i distributionshistoriken |800<sup>1</sup> |800 |
| Resurser per distribution |800 |800 |
| Hanteringslås per unikt |20 |20 |
| Antal taggar per resurs eller resursgrupp |15 |15 |
| Taggen nyckellängd |512 |512 |
| Taggen värdets längd |256 |256 |

<sup>1</sup>om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från historiken över som inte längre behövs. Ta bort en post från distributionshistoriken påverkar inte distribuerade resurser. Du kan ta bort poster från historiken över med [az group deployment ta bort](/cli/azure/group/deployment) för Azure CLI eller [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) i PowerShell.  Ett PowerShell-skript som automatiserar tar bort distributioner i en kontinuerlig integrering och kontinuerlig leverans (CI/CD) scenario finns [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Mall för gränser

| Value | Standardgräns | Övre gräns |
| --- | --- | --- |
| Parametrar |256 |256 |
| Variabler |256 |256 |
| Resurser (inklusive antal kopior) |800 |800 |
| Utdata |64 |64 |
| Malluttryck |24,576 tecken |24,576 tecken |
| Resurser i exporterade mallar |200 |200 | 
| Mallstorlek |1 MB |1 MB |
| Parametern filstorlek |64 kB |64 kB |

Du kan överskrida vissa begränsningar för mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
