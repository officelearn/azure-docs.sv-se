---
title: Kör container-exempel för kommandot Docker Run
titleSuffix: Azure Cognitive Services
description: Docker Run-kommandot för Extrahering av diskussionsämne container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ed42d5b0461b6273c2f8d84a267b65461d160ef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877118"
---
Kör följande `docker run` kommando för att köra *extrahering av diskussionsämne* -behållaren.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *extrahering av diskussionsämne* behållare från behållar avbildningen
* Allokerar en processor kärna och 4 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.