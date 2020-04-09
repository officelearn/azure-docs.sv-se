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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877990"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på riktmärken med en enda begäran per sekund, med en 8 MB-bild av ett skannat affärsbrev som innehåller 29 rader och totalt 803 tecken.

I följande tabell beskrivs den minsta och rekommenderade fördelningen av resurser för varje Läs-behållare.

| Container | Minimum | Rekommenderas |Tps<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
| Läsa | 1 kärnor, 8 GB minne, 0,24 TPS | 8 kärnor, 16 GB minne, 1,17 TPS | 0.24, 1.17 |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS - transaktioner per sekund.

Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.
