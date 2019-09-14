---
title: Docker-hämtning för Extrahering av diskussionsämne container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Extrahering av diskussionsämne container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966749"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-hämtning för Extrahering av diskussionsämne container

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757) container på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
