---
title: Docker-drag för behållaren för språkidentifiering
titleSuffix: Azure Cognitive Services
description: Docker-dragningskommando för behållare för språkidentifiering
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966684"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-drag för behållaren för språkidentifiering

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning från Microsoft Container Registry.

En fullständig beskrivning av tillgängliga taggar för Text Analytics-behållarna finns i behållaren [för språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759) på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
