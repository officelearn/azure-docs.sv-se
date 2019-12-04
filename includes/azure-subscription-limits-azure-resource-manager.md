---
title: ta med fil
description: ta med fil
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: eb2d663a56084fc625c95f1f1a6236385880180d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796151"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella datorer per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> per region. |25 000 per region. |
| Totalt antal VM-kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Kontakta supporten. |
| Totalt antal kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) i Azure-VM |20<sup>1</sup> per region. | Kontakta supporten. |
| VM per serie, till exempel Dv2 och F, kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Kontakta supporten. |
| [Medadministratörer](../articles/billing-add-change-azure-subscription-administrator.md) per prenumeration |Många. |Många. |
| [Lagrings konton](../articles/storage/common/storage-quickstart-create-account.md) per region per prenumeration |250 |250 |
| [Resurs grupper](../articles/azure-resource-manager/resource-group-overview.md) per prenumeration |980 |980 |
| [Tillgänglighets uppsättningar](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per prenumeration |2 000 per region. |2 000 per region. |
| Storlek på Azure Resource Manager API-begäran |4 194 304 byte. |4 194 304 byte. |
| Taggar per prenumeration<sup>2</sup> |Många. |Många. |
| Beräkningar av unika Taggar per prenumeration<sup>2</sup> | 10 000 | 10 000 |
| [Molntjänster](../articles/cloud-services/cloud-services-choose-me.md) per prenumeration |Ej tillämpligt<sup>3</sup> |Ej tillämpligt<sup>3</sup> |
| [Tillhörighetsgrupper](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per prenumeration |Ej tillämpligt<sup>3</sup> |Ej tillämpligt<sup>3</sup> |
| [Distributioner på prenumerations nivå](../articles/azure-resource-manager/deploy-to-subscription.md) per plats | 800<sup>4</sup> | 800 |

<sup>1</sup> Standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version och betala per användning och efter serie, till exempel Dv2, F och G. Standardvärdet för Enterprise-avtal-prenumerationer är till exempel 350.

<sup>2</sup> Du kan använda ett obegränsat antal Taggar per prenumeration. Antalet Taggar per resurs eller resurs grupp är begränsat till 50. Resource Manager returnerar en [lista med unikt taggnamn och värden](/rest/api/resources/tags) i prenumerationen endast om antalet taggar är 10 000 eller mindre. Du kan fortfarande hitta en resurs efter tagg när antalet överskrider 10 000.  

<sup>3</sup> Dessa funktioner krävs inte längre med Azures resurs grupper och Resource Manager.

<sup>4</sup> Om du når gränsen på 800-distributioner tar du bort distributioner från den historik som inte längre behövs. Ta bort distributioner på prenumerations nivå genom att använda [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) eller [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Virtuella dator kärnor har en regional total gräns. De har också en gräns för regionala serier per storlek, till exempel Dv2 och F. Dessa gränser tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 a1-VM: ar eller 30 D1-datorer, eller en kombination av de två som inte överskrider totalt 30 kärnor. Ett exempel på en kombination är 10 a1 VM och 20 D1-datorer.  
> <!-- -->
> 
> 

