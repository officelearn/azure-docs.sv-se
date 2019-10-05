---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 1190798b234f9c73e02fda41c03ffa296246be63
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71975347"
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

<sup>1</sup> Om du når gränsen på 800-distributioner per resurs grupp tar du bort distributioner från den historik som inte längre behövs. Om du tar bort en post från distributions historiken påverkas inte de distribuerade resurserna. Mer information finns i [lösa fel när antalet distributioner överstiger 800](../articles/azure-resource-manager/deployment-quota-exceeded.md).

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
