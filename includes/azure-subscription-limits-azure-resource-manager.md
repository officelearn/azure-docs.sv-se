---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133737"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Resurs grupper](../articles/azure-resource-manager/management/overview.md) per prenumeration |980 |980 |
| Storlek på Azure Resource Manager API-begäran |4 194 304 byte. |4 194 304 byte. |
| Taggar per prenumeration<sup>1</sup> |Många. |Många. |
| Beräkningar av unika Taggar per prenumeration<sup>1</sup> | 10 000 | 10 000 |
| [Distributioner på prenumerations nivå](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per plats | 800<sup>2</sup> | 800 |
| Prenumerationer per Azure Active Directory klient | Många. | Många. |
| [Medadministratörer](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per prenumeration |Många. |Många. |

<sup>1</sup> Du kan använda ett obegränsat antal Taggar per prenumeration. Antalet Taggar per resurs eller resurs grupp är begränsat till 50. Resource Manager returnerar en [lista med unikt taggnamn och värden](/rest/api/resources/tags) i prenumerationen endast om antalet taggar är 10 000 eller mindre. Du kan fortfarande hitta en resurs efter tagg när antalet överskrider 10 000.  

<sup>2</sup> Om du når gränsen på 800-distributioner tar du bort distributioner från den historik som inte längre behövs. Ta bort distributioner på prenumerations nivå genom att använda [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) eller [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
