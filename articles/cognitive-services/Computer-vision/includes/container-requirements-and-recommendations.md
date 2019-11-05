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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481758"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken.

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read-behållare.

| Container | Minimum | Rekommenderas |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Läsa | 1 kärnor, 8 GB minne, 0,24 TPS | 8 kärnor, 16 GB minne, 1,17 TPS | 0,24, 1,17 |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund.

Core och minne motsvarar `--cpus` och `--memory` inställningar som används som en del av `docker run` kommandot.
