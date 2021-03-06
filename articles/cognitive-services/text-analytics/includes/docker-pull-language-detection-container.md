---
title: Docker-hämtning för Språkidentifiering container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Språkidentifiering container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906037"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-hämtning för Språkidentifiering container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759) container på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
