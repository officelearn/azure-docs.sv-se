---
title: Docker dra för Sentiment Analysis container
titleSuffix: Azure Cognitive Services
description: Docker-pull-kommando för sentimentanalysbehållare
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966730"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker dra för Sentiment Analysis container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning från Microsoft Container Registry.

En fullständig beskrivning av tillgängliga taggar för Text Analytics-behållarna finns i behållaren [för sentimentanalys](https://go.microsoft.com/fwlink/?linkid=2018654) på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
