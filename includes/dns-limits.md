---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334722"
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