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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198334"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Tidsgränsen för funktionsappen 

Tidsgränsen för en funktionsapp `functionTimeout` definieras av egenskapen i [projektfilen host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) I följande tabell visas standard- och maxvärdena i minuter för både abonnemang och de olika körningsversionerna:

| Planera | Runtime-version | Default | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2.x | 5 | 10 |
| Förbrukning | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Unlimited |
| Premium | 2.x | 30 | Unlimited |
| Premium | 3.x | 30 | Unlimited |
| App Service | 1.x | Unlimited | Unlimited |
| App Service | 2.x | 30 | Unlimited |
| App Service | 3.x | 30 | Unlimited |

> [!NOTE] 
> Oavsett timeout-inställningen för funktionsappen är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan ta för att svara på en begäran. Detta beror på [standardtidsgränsen för inaktiv utskrift för Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). För längre bearbetningstider bör du överväga att använda [asynkronmönstret Varaktiga funktioner](../articles/azure-functions/durable/durable-functions-overview.md#async-http) eller [skjuta upp det verkliga arbetet och returnera ett omedelbart svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
