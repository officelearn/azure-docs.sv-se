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
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941747"
---
## <a name="timeout"></a>Varaktighet för Function-appen 

Tids gränsen för en Function-app definieras av egenskapen functionTimeout i projekt filen [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . I följande tabell visas standard-och max värden i minuter för båda planerna och i båda körnings versionerna:

| Plan | Körnings version | Standard | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2x | 5 | 10 |
| Förbrukning | 3.x | 5 | 10 |
| App Service | 1.x | Obegränsat | Obegränsat |
| App Service | 2x | 30 | Obegränsat |
| App Service | 3.x | 30 | Obegränsat |

> [!NOTE] 
> Oavsett inställningen för funktionen app-timeout är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan utföra för att svara på en begäran. Detta beror på [förvalda tids gräns för inaktivitet för Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). För längre bearbetnings tider kan du överväga att använda [Durable Functions asynkrona mönstret](../articles/azure-functions/durable/durable-functions-overview.md#async-http) eller [skjuta upp det faktiska arbetet och returnera ett omedelbart svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
