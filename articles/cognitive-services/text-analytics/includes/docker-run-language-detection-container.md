---
title: Kör container-exempel för kommandot Docker Run
titleSuffix: Azure Cognitive Services
description: Docker Run-kommandot för Språkidentifiering container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1f6bfbf3a15f17c3cfd0487cdd0bbb535ffc31ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877042"
---
Kör följande `docker run` kommando för att köra *språkidentifiering* -behållaren.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *språkidentifiering* behållare från behållar avbildningen
* Allokerar en processor kärna och 4 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.