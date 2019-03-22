---
title: ta med fil
description: ta med fil
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305399"
---
## <a name="timeout"></a>Funktionen app timeout-varaktighet 

Tidsgräns för varaktigheten för en funktionsapp definieras av egenskapen functionTimeout i den [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfilen. I följande tabell visar de standard- och högsta värdena i minuter för båda prenumerationerna och båda runtime-versioner:

| Planera | Körningsversion | Standard | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2.x | 5 | 10 |
| App Service | 1.x | Obegränsat | Obegränsat |
| App Service | 2.x | 30 | Obegränsat |

> [!NOTE] 
> Oavsett timeout funktionsappsinställning är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan vidta för att svara på en begäran. Detta är på grund av den [standard timeout för inaktivitet på Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Överväg att använda för längre bearbetningstider den [varaktiga funktioner asynkrona mönstret](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) eller [skjuta upp det faktiska arbetet och returnera ett direkt svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
