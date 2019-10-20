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
ms.openlocfilehash: 47c2429363945cf1529cee6e49947aaea95b7022
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597407"
---
## <a name="timeout"></a>Varaktighet för Function-appen 

Tids gränsen för en Function-app definieras av egenskapen functionTimeout i projekt filen [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . I följande tabell visas standard-och max värden i minuter för båda planerna och i båda körnings versionerna:

| Plan | Körnings version | Standard | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2x | 5 | 10 |
| Förbrukning | 3. x (förhands granskning) | 5 | 10 |
| App Service | 1.x | Obegränsat | Obegränsat |
| App Service | 2x | 30 | Obegränsat |
| App Service | 3. x (förhands granskning) | 30 | Obegränsat |

> [!NOTE] 
> Oavsett inställningen för funktionen app-timeout är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan utföra för att svara på en begäran. Detta beror på [förvalda tids gräns för inaktivitet för Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). För längre bearbetnings tider kan du överväga att använda [Durable Functions asynkrona mönstret](../articles/azure-functions/durable/durable-functions-overview.md#async-http) eller [skjuta upp det faktiska arbetet och returnera ett omedelbart svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
