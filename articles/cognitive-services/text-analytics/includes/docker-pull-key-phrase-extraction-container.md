---
title: Docker-hämtning för Extrahering av diskussionsämne container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Extrahering av diskussionsämne container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877121"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-hämtning för Extrahering av diskussionsämne container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757) container på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
