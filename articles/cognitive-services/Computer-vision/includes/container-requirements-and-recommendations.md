---
title: Krav och rekommendationer för behållare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481758"
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
