---
title: Krav och rekommendationer för behållare.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034478"
---
I följande tabell beskrivs de minsta och rekommenderade processor kärnor och minne som ska allokeras för varje Identifiera text behållare.

| Container | Minimum | Rekommenderas |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Identifiera text|1 kärna, 8 GB minne, 0,5 TPS|2 kärnor, 8 GB minne, 1 TPS|0,5, 1|

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund

Core och minne motsvarar `--cpus` inställningarna och `--memory` som `docker run` används som en del av kommandot.