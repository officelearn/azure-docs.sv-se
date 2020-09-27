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
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397204"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken.

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read-behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Läs 2,0 – för hands version | 1 kärna, 8 GB minne |  8 kärnor, 16 GB minne |
| Läs 3,0 – för hands version | 8 kärnor, 16 GB minne | 8 kärnor, 24 GB minne |
| Läs 3,1 – för hands version | 8 kärnor, 16 GB minne | 8 kärnor, 24 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.
