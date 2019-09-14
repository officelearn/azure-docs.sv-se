---
title: Kör container-exempel för kommandot Docker Run
titleSuffix: Azure Cognitive Services
description: Docker Run-kommandot för Språkidentifiering container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: d2818336de2b3d4d810932cefb21edd95b8cf733
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966738"
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
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.