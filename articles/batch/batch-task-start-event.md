---
title: Starthändelse för uppgift till Azure Batch | Microsoft Docs
description: Referens för Batch Starthändelse för uppgift.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474436"
---
# <a name="task-start-event"></a>Starthändelse för uppgift

 Den här händelsen genereras när en uppgift har schemalagts att starta på en beräkningsnod av scheduler. Observera att om uppgiften är göras eller placeras i kö den här händelsen kommer att genereras igen under samma uppgift, men antalet återförsök och system Uppgiftsversion uppdateras.


 I följande exempel visar innehållet i en uppgift Starthändelse.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Elementnamn|Type|Anteckningar|
|------------------|----------|-----------|
|jobId|String|Id för jobbet som innehåller aktiviteten.|
|id|String|ID för uppgiften.|
|taskType|String|Typ av uppgiften. Detta kan vara som anger det är en job manager-aktivitet ' JobManager ”eller” användare ”som anger det inte är en job manager-aktivitet.|
|systemTaskVersion|Int32|Det här är räknaren interna återförsök för en aktivitet. Batch-tjänsten kan internt gör en uppgift för tillfälliga problem. De här problemen kan omfatta interna schemaläggning fel eller försök att komma tillrätta med compute-noder i ett felaktigt tillstånd.|
|[nodeInfo](#nodeInfo)|Komplex typ|Innehåller information om Beräkningsnoden som aktiviteten kördes.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex typ|Anger att uppgiften är Flerinstansuppgift som kräver flera beräkningsnoder.  Se [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) information.|
|[Begränsningar](#constraints)|Komplex typ|Begränsningar för körning som gäller för den här uppgiften.|
|[executionInfo](#executionInfo)|Komplex typ|Innehåller information om körning av aktiviteten.|

###  <a name="nodeInfo"></a> nodeInfo

|Elementnamn|Type|Anteckningar|
|------------------|----------|-----------|
|poolId|String|Id för poolen som aktiviteten kördes.|
|nodeId|String|Id för noden som aktiviteten kördes.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Elementnamn|Type|Anteckningar|
|------------------|----------|-----------|
|numberOfInstances|Int|Antalet beräkningsnoder som uppgiften kräver.|

###  <a name="constraints"></a> Begränsningar

|Elementnamn|Type|Anteckningar|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Det maximala antalet gånger uppgiften kan göras. Batch-tjänsten försöker en uppgift om dess slutkod inte är noll.<br /><br /> Observera att det här värdet särskilt styr antalet försök. Batch-tjänsten kommer att försöka aktiviteten en gång och försök sedan upp till den här gränsen. Till exempel om maximalt antal försök är 3, Batch försök en uppgift upp till 4 gånger du (ett första försök och 3 nya försök).<br /><br /> Om maximalt antal försök är 0, försöka uppgifter inte igen av Batch-tjänsten.<br /><br /> Om det högsta antalet försök är -1, görs nya försök uppgifter utan begränsning.<br /><br /> Standardvärdet är 0 (inga nya försök).|

###  <a name="executionInfo"></a> executionInfo

|Elementnamn|Type|Anteckningar|
|------------------|----------|-----------|
|RetryCount|Int32|Hur många gånger uppgiften avslutas av Batch-tjänsten. Uppgiften görs om avslutas med en slutkod, upp till den angivna MaxTaskRetryCount|
