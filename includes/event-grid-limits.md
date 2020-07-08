---
title: inkludera fil
description: inkludera fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83721357"
---
Följande begränsningar gäller för Azure Event Grid Systems ämnen och anpassade ämnen, *inte* händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings takt för ett anpassat ämne (ingress) | 5 000 händelser per sekund per ämne |
| Händelse storlek | 1 MB. Åtgärder debiteras i steg om 64 KB. Händelser över 64 KB kommer därför att debiteras som om de var flera händelser. En händelse som är 130 KB skulle till exempel kunna medföra åtgärder som om det var tre separata händelser.  |
| Ämnen per händelse domän | 100 000 |
| Händelse prenumerationer per ämne i en domän | 500 |
| Händelse prenumerationer för domän omfattning | 50 |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser per sekund |
| Händelse domäner per Azure-prenumeration | 100 |
| Anslutningar för privata slut punkter per ämne eller domän | 64 | 
| Regler för IP-brandvägg per ämne eller domän | 16 | 