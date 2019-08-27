---
title: Docker-hämtning för Extrahering av diskussionsämne container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Extrahering av diskussionsämne container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051269"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Hämta Extrahering av diskussionsämne container

Behållar avbildningar för Textanalys finns tillgängliga på Microsoft Container Registry.

| Container | Container Registry/namn på lagrings plats/avbildning |
|-----------|------------|
| Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757) container på Docker Hub.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-hämtning för Extrahering av diskussionsämne container

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
