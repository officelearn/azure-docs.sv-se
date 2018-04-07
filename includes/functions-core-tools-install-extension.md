---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
När du utvecklar funktioner lokalt kan installera du de tillägg som du behöver genom att använda Azure Functions Core verktyg från terminalen eller från en kommandotolk. Följande `func extensions install` installerar Azure Cosmos DB bindningstillägg:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Ersätt `<taget_version>` med en viss version av paketet. Giltiga versioner visas på sidorna enskilda paketet på [NuGet.org](https://nuget.org).
