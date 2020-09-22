---
title: Fråga efter krav
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941929"
---
I frågan använder du `@StartTime` parametern för att hämta mått data för viss tidsstämpel. Detta kommer att ersättas med en `yyyy-MM-ddTHH:mm:ss` format sträng. 

> [!IMPORTANT]
> Se till att endast mått data från **en enda tidstämpel** returneras av frågan. Mått Advisor kommer att köra frågan mot varje tidsstämpel för att hämta motsvarande mått data. Till exempel ska en fråga för ett mått med *daglig* granularitet bara innehålla en enda tidsstämpel, till exempel `2020-06-21T00:00:00Z` när frågan körs en gång. 
