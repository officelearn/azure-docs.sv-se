---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334722"
---
**Offentliga DNS-zoner**

| Resurs | Gräns |
| --- | --- |
| Offentliga DNS-zoner per prenumeration |250 <sup>1</sup> |
| Postuppsättningar per offentlig DNS-zon |10 000 <sup>1</sup> |
| Poster per post som angetts i offentlig DNS-zon |20 |
| Antal aliasposter för en enda Azure-resurs |20|
| Privata DNS-zoner per prenumeration |1000|
| Postuppsättningar per privat DNS-zon |25000|
| Poster per postuppsättning för privata DNS-zoner |20|
| Virtuella nätverkslänkar per privat DNS-zon |1000|
| Länkar till virtuella nätverk per privata DNS-zoner med automatisk registrering aktiverad |100|
| Antal privata DNS-zoner som ett virtuellt nätverk kan länkas till med automatisk registrering aktiverad |1|
| Antal privata DNS-zoner som ett virtuellt nätverk kan länka |1000|
| Antal DNS-frågor som en virtuell dator kan skicka till Azure DNS-matcharen per sekund |500 <sup>2</sup> |
| Maximalt antal DNS-frågor i kö (väntande svar) per virtuell dator |200 <sup>2</sup> |

<sup>1.</sup> Om du behöver öka dessa gränser kontaktar du Azure Support.

<sup>2.</sup> Dessa gränser tillämpas på varje enskild virtuell dator och inte på den virtuella nätverksnivån. DNS-frågor som överskrider dessa gränser tas bort.