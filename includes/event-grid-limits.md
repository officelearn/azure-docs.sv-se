---
title: inkludera fil
description: inkludera fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859622"
---
Följande begränsningar gäller för Azure Event Grid **ämnen** (system, anpassade och partner ämnen). 

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings hastighet för ett anpassat eller ett partner ämne (ingress) | 5 000 händelser/SEK eller 1 MB/SEK (beroende på vad som uppfylls först) |
| Händelse storlek | 1 MB  |
| Anslutningar för privata slut punkter per ämne  | 64 | 
| Regler för IP-brandvägg per ämne | 16 | 

Följande begränsningar gäller för Azure Event Grid **domäner**. 

| Resurs | Gräns |
| --- | --- |
| Ämnen per händelse domän | 100 000 |
| Händelse prenumerationer per ämne i en domän | 500 |
| Händelse prenumerationer för domän omfattning | 50 |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser/SEK eller 1 MB/SEK (beroende på vad som uppfylls först) |
| Händelse domäner per Azure-prenumeration | 100 |
| Anslutningar för privata slut punkter per domän | 64 | 
| Regler för IP-brandvägg per domän | 16 | 


