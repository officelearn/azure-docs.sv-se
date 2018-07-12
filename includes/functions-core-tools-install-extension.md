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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38941458"
---
När du utvecklar funktioner lokalt kan installera du de tillägg som du behöver med hjälp av Azure Functions Core Tools från terminalen eller från en kommandotolk. 

När du har uppdaterat din *function.json* filen för att inkludera alla bindningar som din funktion måste, kör den `func extensions install` i projektmappen. Kommandot läser den *function.json* fil för att se vilka paket som du behöver och installerar dem sedan.

Om du vill installera en viss version av ett paket eller om du vill installera paket innan du redigerar den *function.json* fil ska du använda den `func extensions install` kommandot med namnet på paketet, som visas i följande exempel:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Ersätt `<target_version>` med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org).
