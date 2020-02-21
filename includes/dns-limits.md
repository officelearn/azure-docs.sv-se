---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: b674f8e31eb61328f60bb24866f73d02653b655f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495175"
---
**Offentliga DNS-zoner**

| Resurs | Standardgräns |
| --- | --- |
| Offentlig DNS-zoner per prenumeration |250 <sup>1</sup> |
| Post uppsättningar per offentlig DNS-zon |10 000 <sup>1</sup> |
| Poster per post uppsättning i offentlig DNS-zon |20 |
| Antal Ali poster för en enda Azure-resurs |20|
| Privat DNS zoner per prenumeration |1000|
| Post uppsättningar per privat DNS-zon |25000|
| Poster per post uppsättning för privata DNS-zoner |20|
| Virtual Network länkar per privat DNS-zon |1000|
| Virtuella nätverk länkar per privat DNS-zoner med automatisk registrering aktive rad |100|
| Antal privata DNS-zoner som ett virtuellt nätverk kan kopplas till med automatisk registrering aktive rad |1|
| Antal privata DNS-zoner som ett virtuellt nätverk kan länkas till |1000|
| Antal DNS-frågor som en virtuell dator kan skicka till Azure DNS-lösare per sekund |500 <sup>2</sup> |
| Maximalt antal DNS-frågor i kö (väntar på svar) per virtuell dator |200 <sup>2</sup> |

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.

<sup>2</sup> Dessa gränser tillämpas på varje enskild virtuell dator och inte på nivån för det virtuella nätverket. DNS-frågor som överskrider dessa gränser ignoreras.