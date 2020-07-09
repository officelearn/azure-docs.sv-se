---
title: Kör container-exempel för kommandot Docker Run
titleSuffix: Azure Cognitive Services
description: Docker körnings kommando för hälso container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108945"
---
För att köra behållaren, hitta först dess avbildnings-ID:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Kör följande `docker run` kommando. Ersätt plats hållarna nedan med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Nyckeln till din Textanalys-resurs. Du hittar den på resursens nyckel- **och slut punkts** sida på Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Slut punkten för att få åtkomst till API för textanalys. Du hittar den på resursens nyckel- **och slut punkts** sida på Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | Bild-ID för din behållare. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Indatamängden för behållaren. | Aktivitets`C:\healthcareMount` <br> Linux/MacOS:`/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Det här kommandot:

- Förutsätter att den inmatade katalogen finns på värddatorn
- Kör ett Textanalys för hälso container från behållar avbildningen
- Allokerar 6 processor kärnor och 12 gigabyte (GB) minne
- Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
- Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.
