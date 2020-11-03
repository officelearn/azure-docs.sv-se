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
ms.openlocfilehash: 49c0a2a37fa72cd8c4750b39ff422a90721e3940
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234007"
---
| Resurs | Gräns |
| --- | --- |
| Prenumerationer per Azure Active Directory klient | Obegränsat |
| [Medadministratörer](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per prenumeration |Obegränsat |
| [Resurs grupper](../articles/azure-resource-manager/management/overview.md) per prenumeration |980 |
| Storlek på Azure Resource Manager API-begäran |4 194 304 byte |
| Taggar per prenumeration<sup>1</sup> |50 |
| Beräkningar av unika Taggar per prenumeration<sup>1</sup> | 10 000 |
| [Distributioner på prenumerations nivå](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per plats | 800<sup>2</sup> |

<sup>1</sup> Du kan använda upp till 50 Taggar direkt till en prenumeration. Prenumerationen kan dock innehålla ett obegränsat antal taggar som tillämpas på resurs grupper och resurser i prenumerationen. Antalet Taggar per resurs eller resurs grupp är begränsat till 50. Resource Manager returnerar en [lista med unikt taggnamn och värden](/rest/api/resources/tags) i prenumerationen endast om antalet taggar är 10 000 eller mindre. Du kan fortfarande hitta en resurs efter tagg när antalet överskrider 10 000.  

<sup>2</sup> Om du når gränsen på 800-distributioner tar du bort distributioner som inte längre behövs från historiken. Ta bort distributioner på prenumerations nivå genom att använda [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) eller [AZ Deployment del Delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
