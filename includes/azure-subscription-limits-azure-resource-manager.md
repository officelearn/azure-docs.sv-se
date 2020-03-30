---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334983"
---
| Resurs | Gräns |
| --- | --- |
| Prenumerationer per Azure Active Directory-klient | Obegränsad. |
| [Coadministrators](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnemang |Obegränsad. |
| [Resursgrupper](../articles/azure-resource-manager/management/overview.md) per prenumeration |980 |
| Storlek på AZURE Resource Manager API-begärande |4 194 304 byte. |
| Taggar per prenumeration<sup>1</sup> |50 |
| Unika taggberäkningar per prenumeration<sup>1</sup> | 10 000 |
| [Distributioner på prenumerationsnivå](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per plats | 800<sup>2</sup> |

<sup>1.</sup> Du kan använda upp till 50 taggar direkt på en prenumeration. Prenumerationen kan dock innehålla ett obegränsat antal taggar som tillämpas på resursgrupper och resurser i prenumerationen. Antalet taggar per resurs eller resursgrupp är begränsat till 50. Resource Manager returnerar en [lista med unikt taggnamn och värden](/rest/api/resources/tags) i prenumerationen endast när antalet taggar är 10 000 eller mindre. Du kan fortfarande hitta en resurs efter tagg när antalet överstiger 10 000.  

<sup>2.</sup> Om du når gränsen på 800 distributioner tar du bort distributioner från historiken som inte längre behövs. Om du vill ta bort distributioner på prenumerationsnivå använder du [Ta bort-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) eller [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
