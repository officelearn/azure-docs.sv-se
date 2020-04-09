---
title: Docker-drag för behållaren för språkidentifiering
titleSuffix: Azure Cognitive Services
description: Docker-dragningskommando för behållare för språkidentifiering
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877101"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-drag för behållaren för språkidentifiering

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning från Microsoft Container Registry.

En fullständig beskrivning av tillgängliga taggar för Text Analytics-behållarna finns i behållaren [för språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759) på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
