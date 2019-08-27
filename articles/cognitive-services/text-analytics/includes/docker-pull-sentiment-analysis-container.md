---
title: Docker-hämtning för Attitydanalys container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Attitydanalys container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051385"
---
## <a name="pull-the-sentiment-analysis-container"></a>Hämta Attitydanalys container

Behållar avbildningar för Textanalys finns tillgängliga på Microsoft Container Registry.

| Container | Container Registry/namn på lagrings plats/avbildning |
|-----------|------------|
| Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [Attitydanalys](https://go.microsoft.com/fwlink/?linkid=2018654) container på Docker Hub.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-hämtning för Attitydanalys container

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
