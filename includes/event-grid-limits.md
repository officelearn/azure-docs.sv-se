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
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845951"
---
Följande begränsningar gäller för Azure Event Grid Systems ämnen och anpassade ämnen, *inte* händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings takt för ett anpassat ämne (ingress) | 5 000 händelser per sekund per ämne |
| Publicera begär Anden | 250 per sekund |
| Händelse storlek | 1 MB (debiteras som flera 64 KB-händelser) |

Följande begränsningar gäller endast för händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Ämnen per händelse domän | 100 000 |
| Händelse prenumerationer per ämne i en domän | 500 |
| Händelse prenumerationer för domän omfattning | 50 |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser per sekund |
| Publicera begär Anden | 250 per sekund |
| Händelse domäner per Azure-prenumeration | 100 |