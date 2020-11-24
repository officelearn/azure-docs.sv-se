---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: a9c830025b9ce6c93bf106a8348b0f9dc6521582
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555475"
---
| Resurs | Gräns |
| --- | --- |
| Virtuella datorer per [prenumeration](https://azure.microsoft.com/pricing/) |25 000<sup>1</sup> per region. |
| Totalt antal VM-kärnor per [prenumeration](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| Totalt antal kärnor per [prenumeration](https://azure.microsoft.com/pricing/) i Azure-VM |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| VM per serie, till exempel Dv2 och F, kärnor per [prenumeration](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| [Tillgänglighets uppsättningar](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per prenumeration |2 500 per region. |
| Virtuella datorer per tillgänglighets uppsättning | 200 |
| Certifikat per prenumeration |Obegränsat<sup>2</sup> |

<sup>1</sup> Standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version och betala per användning och efter serie, till exempel Dv2, F och G. Standardvärdet för Enterprise-avtal-prenumerationer är till exempel 350.

<sup>2</sup> Med Azure Resource Manager lagras certifikat i Azure Key Vault. Antalet certifikat är obegränsat för en prenumeration. Det finns en gräns på 1 MB för certifikat per distribution, som består av antingen en enskild virtuell dator eller en tillgänglighets uppsättning.

> [!NOTE]
> Virtuella dator kärnor har en regional total gräns. De har också en gräns för regionala serier per storlek, till exempel Dv2 och F. Dessa gränser tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 a1-VM: ar eller 30 D1-datorer, eller en kombination av de två som inte överskrider totalt 30 kärnor. Ett exempel på en kombination är 10 a1 VM och 20 D1-datorer.  
> <!-- -->
>