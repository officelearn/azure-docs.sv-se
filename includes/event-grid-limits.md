---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131643"
---
Följande begränsningar gäller för Azure Event Grid Systems ämnen och anpassade ämnen, *inte* händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings takt för ett anpassat ämne (ingress) | 5 000 händelser per sekund per ämne |
| Publicera begär Anden | 250 per sekund |
| Händelse storlek | 1 MB. Åtgärder debiteras i steg om 64 KB. Händelser över 64 KB kommer därför att debiteras som om de var flera händelser. En händelse som är 130 KB skulle till exempel kunna medföra åtgärder som om det var tre separata händelser.  |

Följande begränsningar gäller endast för händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Ämnen per händelse domän | 100 000 |
| Händelse prenumerationer per ämne i en domän | 500 |
| Händelse prenumerationer för domän omfattning | 50 |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser per sekund |
| Publicera begär Anden | 250 per sekund |
| Händelse domäner per Azure-prenumeration | 100 |