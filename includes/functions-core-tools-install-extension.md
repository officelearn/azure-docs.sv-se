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
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726895"
---
När du utvecklar funktioner lokalt kan installera du de tillägg som du behöver genom att använda Azure Functions Core verktyg från terminalen eller från en kommandotolk. 

När du har uppdaterat din *function.json* filen för att inkludera alla bindningar som din funktion måste, kör den `func extensions install` i projektmappen. Kommandot läser den *function.json* filen för att se vilka paket som du behöver och installerar dem sedan.

Om du vill installera en viss version av ett paket eller om du vill installera paket innan du redigerar den *function.json* fil ska du använda den `func extensions install` kommandot med namnet på paketet, som visas i följande exempel:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Ersätt `<target_version>` med en viss version av paketet. Giltiga versioner visas på sidorna enskilda paketet på [NuGet.org](https://nuget.org).
