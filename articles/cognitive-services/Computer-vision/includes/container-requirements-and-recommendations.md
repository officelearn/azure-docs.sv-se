---
title: Krav och rekommendationer för behållare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877990"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken.

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read-behållare.

| Container | Minimum | Rekommenderas |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Läsa | 1 kärnor, 8 GB minne, 0,24 TPS | 8 kärnor, 16 GB minne, 1,17 TPS | 0,24, 1,17 |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund.

Core och minne motsvarar inställningarna `--cpus` och `--memory` som används som en del av `docker run` kommandot.
