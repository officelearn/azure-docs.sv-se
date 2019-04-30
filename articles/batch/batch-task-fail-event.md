---
title: Händelse för misslyckad Azure Batch uppgift | Microsoft Docs
description: Referens för Batch-aktiviteter misslyckas händelse.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/15/2018
ms.author: v-junlch
ms.openlocfilehash: f37769ceb761b8c8bc4834568813bb1b7af7f66a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549996"
---
# <a name="task-fail-event"></a>Händelse för misslyckad uppgift

 Den här händelsen genereras när en uppgift har slutförts med fel. Alla avslutningskoder som inte är noll beaktas för närvarande är fel. Den här händelsen genereras *förutom* en uppgift Slutför händelse och kan användas för att identifiera när en uppgift misslyckades.


 I följande exempel visar innehållet i en aktivitet misslyckas händelse.

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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|jobId|String|Id för jobbet som innehåller aktiviteten.|
|id|String|ID för uppgiften.|
|taskType|String|Typ av uppgiften. Detta kan vara som anger det är en job manager-aktivitet ' JobManager ”eller” användare ”som anger det inte är en job manager-aktivitet. Den här händelsen genereras inte för jobbförberedelse, jobbpubliceringsaktiviteter eller startaktiviteter.|
|systemTaskVersion|Int32|Det här är räknaren interna återförsök för en aktivitet. Batch-tjänsten kan internt gör en uppgift för tillfälliga problem. De här problemen kan omfatta interna schemaläggning fel eller försök att komma tillrätta med compute-noder i ett felaktigt tillstånd.|
|[nodeInfo](#nodeInfo)|Komplex typ|Innehåller information om Beräkningsnoden som aktiviteten kördes.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex typ|Anger att uppgiften är en Flerinstansuppgift som kräver flera beräkningsnoder.  Se [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) information.|
|[Begränsningar](#constraints)|Komplex typ|Begränsningar för körning som gäller för den här uppgiften.|
|[executionInfo](#executionInfo)|Komplex typ|Innehåller information om körning av aktiviteten.|

###  <a name="nodeInfo"></a> nodeInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|poolId|String|Id för poolen som aktiviteten kördes.|
|nodeId|String|Id för noden som aktiviteten kördes.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|numberOfInstances|Int32|Antalet beräkningsnoder som uppgiften kräver.|

###  <a name="constraints"></a> Begränsningar

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Det maximala antalet gånger uppgiften kan göras. Batch-tjänsten försöker en uppgift om dess slutkod inte är noll.<br /><br /> Observera att det här värdet särskilt styr antalet försök. Batch-tjänsten kommer att försöka aktiviteten en gång och försök sedan upp till den här gränsen. Till exempel om maximalt antal försök är 3, Batch försök en uppgift upp till 4 gånger du (ett första försök och 3 nya försök).<br /><br /> Om maximalt antal försök är 0, försöka uppgifter inte igen av Batch-tjänsten.<br /><br /> Om det högsta antalet försök är -1, görs nya försök uppgifter utan begränsning.<br /><br /> Standardvärdet är 0 (inga nya försök).|


###  <a name="executionInfo"></a> executionInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|startTime|DateTime|Tidpunkten då aktiviteten startades som körs. ”Kör” motsvarar den **kör** tillstånd, så om aktiviteten anger resursfiler eller programpaket, återspeglar starttiden tidpunkten då aktiviteten startades ladda ned eller distribution av dessa.  Om uppgiften har startats om eller göras, är de senaste tidpunkten då aktiviteten startades som körs.|
|endTime|DateTime|Den tid då uppgiften är slutförd.|
|Slutkod|Int32|Slutkoden för uppgiften.|
|RetryCount|Int32|Hur många gånger uppgiften avslutas av Batch-tjänsten. Uppgiften görs om avslutas med en slutkod, upp till den angivna MaxTaskRetryCount.|
|requeueCount|Int32|Antal gånger som aktiviteten har åter placerats i kö av Batch-tjänsten som ett resultat av en användarbegäran.<br /><br /> När användaren tar bort noder från en pool (genom att ändra storlek på eller minska storleken på poolen) eller när jobbet är inaktiverad och användaren kan ange att uppgifter som körs på noderna ska placeras i kö för körning. Det här antalet spårar hur många gånger uppgiften har åter placerats i kö av dessa skäl.|

<!-- Update_Description: update metedata properties -->