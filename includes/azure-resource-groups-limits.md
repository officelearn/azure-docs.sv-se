---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: f0ab7c2efc499c43245680e56a7e5ca1b5261397
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748799"
---
| Resurs | Gräns |
| --- | --- |
| Resurser per [resurs grupp](../articles/azure-resource-manager/management/overview.md#resource-groups) | Resurserna begränsas inte av resurs gruppen. De är i stället begränsade av resurs typen i en resurs grupp. Se nästa rad. |
| Resurser per resurs grupp, per resurs typ |800 – vissa resurs typer kan överskrida gränsen på 800. Se [resurser som inte är begränsade till 800 instanser per resurs grupp](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Distributioner per resurs grupp i distributions historiken |800<sup>1</sup> |
| Resurser per distribution |800 |
| Hanterings lås per unik [omfattning](../articles/azure-resource-manager/management/overview.md#understand-scope)  |20 |
| Antal Taggar per resurs eller resurs grupp |50 |
| Etikett nyckel längd |512 |
| Tagg värde längd |256 |

<sup>1</sup> Från och med juni 2020 tas distributioner automatiskt bort från historiken när du närmar dig gränsen. Om du tar bort en post från distributions historiken påverkas inte de distribuerade resurserna. Mer information finns i [automatiska borttagningar från distributions historiken](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Mall gränser

| Värde | Gräns |
| --- | --- |
| Parametrar |256 |
| Variabler |256 |
| Resurser (inklusive antal exemplar) |800 |
| Utdata |64 |
| Mall uttryck |24 576 tecken |
| Resurser i exporterade mallar |200 |
| Mallens storlek |4 MB |
| Parameter fil storlek |64 kB |

Du kan överskrida vissa begränsningar för mallar genom att använda en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/templates/linked-templates.md). Om du vill minska antalet parametrar, variabler eller utdata kan du kombinera flera värden i ett objekt. Mer information finns i [objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
