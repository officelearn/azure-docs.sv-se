---
title: Behållarkrav och rekommendationer
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129805"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken.

#### <a name="readtabread"></a>[Läsa](#tab/read)

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read-behållare.

| Container | Minimum | Rekommenderas |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Läsa | 1 kärnor, 8 GB minne, 0,24 TPS | 8 kärnor, 16 GB minne, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Identifiera Text](#tab/recognize-text)

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje Identifiera text behållare.

| Container | Minimum | Rekommenderas |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Identifiera text | 1 kärna, 8 GB minne, 0,12 TPS | 8 kärnor, 16 GB minne, 0,60 TPS | 0,12, 0,60 |

***

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund.

Core och minne motsvarar `--cpus` inställningarna och `--memory` som `docker run` används som en del av kommandot.
