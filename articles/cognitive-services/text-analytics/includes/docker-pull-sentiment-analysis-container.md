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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877061"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-hämtning för Attitydanalys container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [Attitydanalys](https://go.microsoft.com/fwlink/?linkid=2018654) container på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
