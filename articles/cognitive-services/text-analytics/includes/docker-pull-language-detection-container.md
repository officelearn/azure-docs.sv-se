---
title: Docker-hämtning för Språkidentifiering container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Språkidentifiering container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051261"
---
## <a name="pull-the-language-detection-container"></a>Hämta Språkidentifiering container

Behållar avbildningar för Textanalys finns tillgängliga på Microsoft Container Registry.

| Container | Container Registry/namn på lagrings plats/avbildning |
|-----------|------------|
| Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759) container på Docker Hub.


### <a name="docker-pull-for-the-language-detection-container"></a>Docker-hämtning för Språkidentifiering container

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
