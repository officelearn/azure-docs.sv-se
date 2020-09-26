---
title: Fråga efter krav
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376492"
---
I frågan använder du `@StartTime` parametern för att hämta mått data för viss tidsstämpel. Detta kommer att ersättas med en `yyyy-MM-ddTHH:mm:ss` format sträng. 

> [!IMPORTANT]
> Se till att endast mått data från **en enda tidstämpel** returneras av frågan. Mått Advisor kommer att köra frågan mot varje tidsstämpel för att hämta motsvarande mått data. Till exempel ska en fråga för ett mått med *daglig* granularitet bara innehålla en enda tidsstämpel, till exempel `2020-06-21T00:00:00Z` när frågan körs en gång. 
