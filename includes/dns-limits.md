---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 434b79a2b178defd9543e1d3ad087bb5282cb287
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805617"
---
**Offentliga DNS-zoner**

| Resurs | Gräns |
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
