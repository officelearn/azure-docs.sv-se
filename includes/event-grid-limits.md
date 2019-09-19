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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "66376927"
---
Följande begränsningar gäller för Azure Event Grid Systems ämnen och anpassade ämnen, *inte* händelse domäner.

| Resource | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings takt för ett anpassat ämne (ingress) | 5 000 händelser per sekund per ämne |
| Publicera begär Anden | 250 per sekund |
| Händelse storlek | Stöd för 64 KB i allmän tillgänglighet (GA). Stöd för 1 MB är för närvarande en för hands version. |

Följande begränsningar gäller endast för händelse domäner.

| Resource | Gräns |
| --- | --- |
| Ämnen per händelse domän | 1 000 under offentlig för hands version |
| Händelse prenumerationer per ämne i en domän | 50 under offentlig för hands version |
| Händelse prenumerationer för domän omfattning | 50 under offentlig för hands version |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser per sekund under offentlig för hands version |
| Publicera begär Anden | 250 per sekund |