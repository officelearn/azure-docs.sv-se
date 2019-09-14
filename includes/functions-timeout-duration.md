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
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963595"
---
## <a name="timeout"></a>Varaktighet för Function-appen 

Tids gränsen för en Function-app definieras av egenskapen functionTimeout i projekt filen [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . I följande tabell visas standard-och max värden i minuter för båda planerna och i båda körnings versionerna:

| Planera | Körnings version | Standard | Maximal |
|------|---------|---------|---------|
| Förbrukning | 1.x | 5 | 10 |
| Förbrukning | 2x | 5 | 10 |
| App Service | 1.x | Obegränsat | Obegränsat |
| App Service | 2x | 30 | Obegränsat |

> [!NOTE] 
> Oavsett inställningen för funktionen app-timeout är 230 sekunder den maximala tid som en HTTP-utlöst funktion kan utföra för att svara på en begäran. Detta beror på [förvalda tids gräns för inaktivitet för Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). För längre bearbetnings tider kan du överväga att använda [Durable Functions asynkrona mönstret](../articles/azure-functions/durable/durable-functions-overview.md#async-http) eller [skjuta upp det faktiska arbetet och returnera ett omedelbart svar](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
