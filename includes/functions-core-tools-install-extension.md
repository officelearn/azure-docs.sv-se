---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044517"
---
När du utvecklar funktioner lokalt kan installera du de tillägg som du behöver med hjälp av Azure Functions Core Tools från terminalen eller från en kommandotolk.

När du har uppdaterat din *function.json* filen för att inkludera alla bindningar som din funktion behöver, kör följande kommando i projektmappen.

```bash
func extensions install
```

Kommandot läser den *function.json* fil för att se vilka paket som du behöver, installerar dem och återskapar tillägg-projektet. Den lägger till några nya bindningar på den aktuella versionen men uppdaterar inte befintliga bindningar. Använd den `--force` alternativet för att uppdatera befintliga bindningar till den senaste versionen när du installerar nya.

Om du vill installera en viss version av ett paket eller om du vill installera paket innan du redigerar den *function.json* fil ska du använda den `func extensions install` kommandot med namnet på paketet, som visas i följande exempel:

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Ersätt `<target_version>` med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org).
