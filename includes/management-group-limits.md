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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334778"
---
| Resurs | Gräns |
| --- | --- |
| Hanterings grupper per katalog | 10 000 |
| Prenumerationer per hanterings grupp | Många. |
| Nivåer för hanterings gruppens hierarki | Rot nivå plus 6 nivåer<sup>1</sup> |
| Direkt överordnad hanterings grupp per hanterings grupp | En |
| [Distributioner av hanterings grupp nivå](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per plats | 800<sup>2</sup> |

<sup>1</sup> 6 nivåer omfattar inte prenumerations nivån.

<sup>2</sup> Om du når gränsen på 800-distributioner tar du bort distributioner från den historik som inte längre behövs. Ta bort distributioner på hanterings grupps nivå genom att använda [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) eller [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
