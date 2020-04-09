---
title: Docker dra för Sentiment Analysis container
titleSuffix: Azure Cognitive Services
description: Docker-pull-kommando för sentimentanalysbehållare
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877061"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker dra för Sentiment Analysis container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning från Microsoft Container Registry.

En fullständig beskrivning av tillgängliga taggar för Text Analytics-behållarna finns i behållaren [för sentimentanalys](https://go.microsoft.com/fwlink/?linkid=2018654) på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
