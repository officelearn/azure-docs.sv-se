---
title: Docker dra för nyckelfrasutextraktion behållare
titleSuffix: Azure Cognitive Services
description: Docker-dragningskommando för behållare för extraktion av nyckelfraser
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877121"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker dra för nyckelfrasutextraktion behållare

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning från Microsoft Container Registry.

En fullständig beskrivning av tillgängliga taggar för Text Analytics-behållarna finns i behållaren för [extrahering av nyckelfraser](https://go.microsoft.com/fwlink/?linkid=2018757) på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
