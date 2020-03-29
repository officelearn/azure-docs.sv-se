---
title: Docker dra för nyckelfrasutextraktion behållare
titleSuffix: Azure Cognitive Services
description: Docker-dragningskommando för behållare för extraktion av nyckelfraser
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966749"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker dra för nyckelfrasutextraktion behållare

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning från Microsoft Container Registry.

En fullständig beskrivning av tillgängliga taggar för Text Analytics-behållarna finns i behållaren för [extrahering av nyckelfraser](https://go.microsoft.com/fwlink/?linkid=2018757) på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
