---
title: Docker-hämtning för Attitydanalys container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Attitydanalys container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588428"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-hämtning för Attitydanalys v3-behållaren

Sentiment Analysis container v3-behållaren finns på flera olika språk. Om du vill ladda ned behållaren för den engelska behållaren använder du kommandot nedan. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Om du vill hämta behållaren för ett annat språk ersätter du `en` med en av språk koderna nedan. 

| Textanalys behållare | Språkkod |
|--|--|
| Engelska | `en` |
| Spanska | `es` |
| Franska | `fr` |
| Italienska | `it` |
| Tyska | `de` |
| Kinesiska – Förenklad | `zh` |
| Kinesiska (traditionell) | `zht` |
| Japanska | `ja` |
| Portugisiska | `pt` |
| Nederländska | `nl` |

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).