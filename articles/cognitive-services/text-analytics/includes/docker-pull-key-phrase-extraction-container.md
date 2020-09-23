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
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906054"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-hämtning för Extrahering av diskussionsämne container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757) container på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
