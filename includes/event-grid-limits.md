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
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376927"
---
Följande begränsningar gäller för Azure Event Grid system ämnen och anpassade ämnen *inte* händelse domäner.

| Resource | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelseprenumerationer per ämne | 500 |
| Publicera priset för ett anpassat ämne (inkommande) | 5 000 händelser per sekund per ämne |
| Publicera begäranden | 250 per sekund |
| Händelsestorlek | Stöd för 64 KB allmänt tillgänglig (GA). Stöd för 1 MB förhandsvisas just nu. |

Följande begränsningar gäller för händelse-domäner.

| Resource | Gräns |
| --- | --- |
| Ämnen per event domän | 1 000 allmänt tillgängliga förhandsversionen |
| Händelseprenumerationer per avsnitt inom en domän | 50 allmänt tillgängliga förhandsversionen |
| Händelseprenumerationer för domänen omfång | 50 allmänt tillgängliga förhandsversionen |
| Publicera priset för en händelsedomän (inkommande) | 5 000 händelser per sekund under den allmänna förhandsvisningen |
| Publicera begäranden | 250 per sekund |