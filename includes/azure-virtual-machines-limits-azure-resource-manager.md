---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82616038"
---
| Resurs | Gräns |
| --- | --- |
| Virtuella datorer per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> per region. |
| Totalt antal VM-kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| Totalt antal kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) i Azure-VM |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| VM per serie, till exempel Dv2 och F, kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| [Tillgänglighets uppsättningar](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per prenumeration |2 500 per region. |
| Virtuella datorer per tillgänglighets uppsättning | 200 |
| Certifikat per prenumeration |Obegränsat<sup>2</sup> |

<sup>1</sup> Standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version och betala per användning och efter serie, till exempel Dv2, F och G. Standardvärdet för Enterprise-avtal-prenumerationer är till exempel 350.

<sup>2</sup> Med Azure Resource Manager lagras certifikat i Azure Key Vault. Antalet certifikat är obegränsat för en prenumeration. Det finns en gräns på 1 MB för certifikat per distribution, som består av antingen en enskild virtuell dator eller en tillgänglighets uppsättning.

> [!NOTE]
> Virtuella dator kärnor har en regional total gräns. De har också en gräns för regionala serier per storlek, till exempel Dv2 och F. Dessa gränser tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 a1-VM: ar eller 30 D1-datorer, eller en kombination av de två som inte överskrider totalt 30 kärnor. Ett exempel på en kombination är 10 a1 VM och 20 D1-datorer.  
> <!-- -->
> 
