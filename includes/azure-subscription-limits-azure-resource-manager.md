---
title: ta med fil
description: ta med fil
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/02/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d490cab4d437c30fdb211ea27397777afc27e72e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891112"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella datorer per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> per region. |25 000 per region. |
| Totalt antal VM-kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Kontakta supporten. |
| VM per serie, t.ex Dv2 och F-kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Kontakta supporten. |
| [Medadministratörer](../articles/billing-add-change-azure-subscription-administrator.md) per prenumeration |Obegränsad. |Obegränsad. |
| [Storage-konton](../articles/storage/common/storage-quickstart-create-account.md) per region per prenumeration |250 |250 |
| [Resursgrupper](../articles/azure-resource-manager/resource-group-overview.md) per prenumeration |980 |980 |
| [Tillgänglighetsuppsättningar](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per prenumeration |2 000 per region. |2 000 per region. |
| Storlek på Azure Resource Manager-API-begäran |4,194,304 byte. |4,194,304 byte. |
| Taggar per prenumeration<sup>2</sup> |Obegränsad. |Obegränsad. |
| Unika taggberäkningar per prenumeration<sup>2</sup> | 10 000 | 10 000 |
| [Molntjänster](../articles/cloud-services/cloud-services-choose-me.md) per prenumeration |EJ TILLÄMPLIGT<sup>3</sup> |EJ TILLÄMPLIGT<sup>3</sup> |
| [Tillhörighetsgrupper](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per prenumeration |EJ TILLÄMPLIGT<sup>3</sup> |EJ TILLÄMPLIGT<sup>3</sup> |
| [Prenumerationsnivå distributioner](../articles/azure-resource-manager/deploy-to-subscription.md) per plats | 800 | 800 |

<sup>1</sup>standardgränserna varierar genom erbjudande kategorityp, t.ex kostnadsfri utvärderingsversion och betala per användning, och serie, t.ex Dv2, F och G.

<sup>2</sup>kan du använda ett obegränsat antal taggar per prenumeration. Antalet taggar per resurs eller resursgrupp är begränsat till 15. Resource Manager returnerar en [lista med unika taggnamn och taggvärden](/rest/api/resources/tags) i prenumerationen endast när antalet taggar är 10 000 eller mindre. Du kan fortfarande hitta en resurs genom att tagga när fler än 10 000.  

<sup>3</sup>dessa funktioner krävs inte längre med Azure-resursgrupper och Resource Manager.

> [!NOTE]
> Virtuella datorkärnor har en regional total gräns. De har också en gräns för regionala per storlek-serien, t.ex Dv2 och F. Dessa begränsningar tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 virtuella datorer i A1 eller 30 D1 virtuella datorer eller en kombination av båda för att inte överstiger sammanlagt 30 kärnor. Ett exempel på en kombination är 10 virtuella datorer i A1 och 20 D1 virtuella datorer.  
> <!-- -->
> 
> 

