---
title: Kör behållarexempel på kommandot docker run
titleSuffix: Azure Cognitive Services
description: Docker kör kommando för Sentiment Analysis container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f0e587fc39fa2cc6f5275ae16834372a206b37d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966779"
---
Kör följande `docker run` kommando om du vill köra behållaren *Sentiment analysis.*

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *sentimentanalysbehållare* från behållaravbildningen
* Allokerar en CPU-kärna och 4 GIGABYTE (GB) minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.