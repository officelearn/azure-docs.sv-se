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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845951"
---
Följande begränsningar gäller för Azure Event Grid-systemavsnitt och anpassade ämnen, *inte* händelsedomäner.

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelseprenumerationer per ämne | 500 |
| Publiceringshastighet för ett anpassat ämne (ingående) | 5 000 händelser per sekund per ämne |
| Publicera begäranden | 250 per sekund |
| Händelsestorlek | 1 MB (debiteras som flera 64 KB-händelser) |

Följande gränser gäller endast för händelsedomäner.

| Resurs | Gräns |
| --- | --- |
| Ämnen per händelsedomän | 100 000 |
| Händelseprenumerationer per ämne inom en domän | 500 |
| Domänomfattningshändelseprenumerationer | 50 |
| Publiceringshastighet för en händelsedomän (ingående) | 5 000 händelser per sekund |
| Publicera begäranden | 250 per sekund |
| Händelsedomäner per Azure-prenumeration | 100 |