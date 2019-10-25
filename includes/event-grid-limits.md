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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887844"
---
Följande begränsningar gäller för Azure Event Grid Systems ämnen och anpassade ämnen, *inte* händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings takt för ett anpassat ämne (ingress) | 5 000 händelser per sekund per ämne |
| Publicera begär Anden | 250 per sekund |
| Händelse storlek | Stöd för 64 KB i allmän tillgänglighet (GA). Stöd för 1 MB är för närvarande en för hands version. |

Följande begränsningar gäller endast för händelse domäner.

| Resurs | Gräns |
| --- | --- |
| Ämnen per händelse domän | 100 000 |
| Händelse prenumerationer per ämne i en domän | 500 |
| Händelse prenumerationer för domän omfattning | 50 |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser per sekund |
| Publicera begär Anden | 250 per sekund |
| Händelse domäner per Azure-prenumeration | 100 |