---
title: ta med fil
description: ta med fil
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334778"
---
| Resurs | Gräns |
| --- | --- |
| Hanteringsgrupper per katalog | 10 000 |
| Prenumerationer per ledningsgrupp | Obegränsad. |
| Nivåer i hanteringsgrupphierarkin | Rotnivå plus 6 nivåer<sup>1</sup> |
| Direkt överordnad ledningsgrupp per ledningsgrupp | En |
| [Distributioner på hanteringsgruppsnivå](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per plats | 800<sup>2</sup> |

<sup>1.</sup> De 6 nivåerna inkluderar inte prenumerationsnivån.

<sup>2.</sup> Om du når gränsen på 800 distributioner tar du bort distributioner från historiken som inte längre behövs. Om du vill ta bort distributioner på hanteringsgruppsnivå använder du [Ta bort-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) eller [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
