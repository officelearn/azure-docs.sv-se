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
ms.openlocfilehash: 189683a9e98f161ce537284cc7b0349c94be2bf0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410918"
---
## <a name="timeout"></a>Funktionen app timeout-varaktighet 

Tidsgräns för varaktigheten för en funktionsapp definieras av egenskapen functionTimeout i den [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) projektfilen. I följande tabell visas standard och högsta värden för båda prenumerationerna och båda runtime-versioner:

| Planera | Körningsversion | Standard | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2.x | 5 | 10 |
| App Service | 1.x | Obegränsat | Obegränsat |
| App Service | 2.x | 30 | Obegränsat |

> [!NOTE] 
> Oavsett timeout funktionsappsinställning är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan vidta för att svara på en begäran. Detta är på grund av den [standard timeout för inaktivitet på Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Överväg att använda för längre bearbetningstider den [varaktiga funktioner asynkrona mönstret](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) eller [skjuta upp det faktiska arbetet och returnera ett direkt svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
