---
title: inkludera fil
description: inkludera fil
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748816"
---
| Resurs | Gräns |
| --- | --- |
| Hanterings grupper per Azure AD-klient | 10 000 |
| Prenumerationer per hanterings grupp | Många. |
| Nivåer för hanterings gruppens hierarki | Rot nivå plus 6 nivåer<sup>1</sup> |
| Direkt överordnad hanterings grupp per hanterings grupp | En |
| [Distributioner av hanterings grupp nivå](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per plats | 800<sup>2</sup> |

<sup>1</sup> 6 nivåer omfattar inte prenumerations nivån.

<sup>2</sup> Om du når gränsen på 800-distributioner tar du bort distributioner från den historik som inte längre behövs. Ta bort distributioner på hanterings grupps nivå genom att använda [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) eller [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
