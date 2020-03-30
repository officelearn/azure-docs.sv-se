---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334767"
---
| Resurs | Gräns |
| --- | --- |
| Resurser per [resursgrupp](../articles/azure-resource-manager/management/overview.md#resource-groups) | Resurserna begränsas inte av resursgruppen. I stället begränsas de av resurstyp i en resursgrupp. Se nästa rad. |
| Resurser per resursgrupp, per resurstyp |800 - Vissa resurstyper kan överskrida gränsen på 800. Se [Resurser som inte är begränsade till 800 instanser per resursgrupp](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Distributioner per resursgrupp i distributionshistoriken |800<sup>1</sup> |
| Resurser per distribution |800 |
| Hanteringslås per unikt scope |20 |
| Antal taggar per resurs eller resursgrupp |50 |
| Taggnyckellängd |512 |
| Taggvärdeslängd |256 |

<sup>1.</sup> Om du når gränsen på 800 distributioner per resursgrupp tar du bort distributioner från historiken som inte längre behövs. Om du tar bort en post från distributionshistoriken påverkas inte de distribuerade resurserna. Mer information finns i [Lös fel när antalet distributioner överstiger 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Mallgränser

| Värde | Gräns |
| --- | --- |
| Parametrar |256 |
| Variabler |256 |
| Resurser (inklusive antal kopior) |800 |
| Utdata |64 |
| Malluttryck |24 576 tecken |
| Resurser i exporterade mallar |200 |
| Mallstorlek |4 MB |
| Parameterfilstorlek |64 kB |

Du kan överskrida vissa mallgränser med hjälp av en kapslad mall. Mer information finns i [Använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/templates/linked-templates.md). Om du vill minska antalet parametrar, variabler eller utdata kan du kombinera flera värden till ett objekt. Mer information finns i [Objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
