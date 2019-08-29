---
title: Azure Batch aktivitet, händelse som misslyckats | Microsoft Docs
description: Referens för händelse vid misslyckad batch-aktivitet.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: lahugh
ms.openlocfilehash: 0a96bbbd9166d501e6824a8875e9505a4a715e8b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094418"
---
# <a name="task-fail-event"></a>Händelse för misslyckad uppgift

 Den här händelsen genereras när en aktivitet slutförs med ett haveri. Alla avslutnings koder som inte är noll betraktas som felaktiga. Den här händelsen kommer att genereras *förutom* en aktivitet som slutförs och kan användas för att identifiera när en aktivitet har misslyckats.


 I följande exempel visas bröd texten i en aktivitet som inte går att logga.

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

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|jobId|Sträng|ID för jobbet som innehåller uppgiften.|
|id|Sträng|Aktivitetens ID.|
|taskType|Sträng|Aktivitetens typ. Detta kan antingen vara ' JobManager ' som indikerar att det är en Job Manager-uppgift eller ' användare ' som indikerar att den inte är en Job Manager-aktivitet. Den här händelsen genereras inte för jobb förberedelse aktiviteter, jobb publicerings aktiviteter eller start uppgifter.|
|systemTaskVersion|Int32|Detta är den interna återförsöks räknaren för en aktivitet. Internt kan batch-tjänsten göra ett nytt försök med en uppgift för att redovisa tillfälliga problem. De här problemen kan omfatta interna schemaläggnings fel eller försök att återställa från datornoder i ett felaktigt tillstånd.|
|[nodeInfo](#nodeInfo)|Komplex typ|Innehåller information om den beräknings nod som aktiviteten kördes på.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex typ|Anger att aktiviteten är en multi-instance-aktivitet som kräver flera datornoder.  Se [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) för mer information.|
|[begränsningar](#constraints)|Komplex typ|De körnings begränsningar som gäller för den här uppgiften.|
|[executionInfo](#executionInfo)|Komplex typ|Innehåller information om körningen av uppgiften.|

###  <a name="nodeInfo"></a>nodeInfo

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|poolId|Sträng|ID för den pool där aktiviteten kördes.|
|nodeId|Sträng|ID: t för noden som aktiviteten kördes på.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|numberOfInstances|Int32|Antalet Compute-noder som aktiviteten kräver.|

###  <a name="constraints"></a>begränsningar

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Det maximala antalet gånger som aktiviteten kan göras om. Batch-tjänsten försöker igen med en aktivitet om dess slutkod är skilt från noll.<br /><br /> Observera att det här värdet specifikt styr antalet återförsök. Batch-tjänsten kommer att försöka utföra åtgärden en gång och kan sedan försöka igen till den här gränsen. Om till exempel det maximala antalet försök är 3, försöker batch utföra en aktivitet upp till fyra gånger (ett första försök och tre försök).<br /><br /> Om det maximala antalet försök är 0, gör batch-tjänsten inte om aktiviteterna.<br /><br /> Om det maximala antalet försök är-1, kommer batch-tjänsten att försöka utföra aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga återförsök).|


###  <a name="executionInfo"></a>executionInfo

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|startTime|DateTime|Tiden då uppgiften startade. "Körs" motsvarar körnings tillstånd , så om aktiviteten anger resursfiler eller programpaket, visar start tiden tiden då uppgiften startade nedladdningen eller distributionen av dessa.  Om aktiviteten har startats om eller gjorts om, är det här den senaste tiden då uppgiften startades.|
|endTime|DateTime|Tiden då uppgiften slutfördes.|
|exitCode|Int32|Avslutnings koden för aktiviteten.|
|retryCount|Int32|Antalet gånger som aktiviteten har fått nytt försök av batch-tjänsten. Aktiviteten provas igen om den avslutas med en slutkod som inte är noll, upp till den angivna MaxTaskRetryCount.|
|requeueCount|Int32|Antalet gånger som aktiviteten har ställts i kö av batch-tjänsten som ett resultat av en användarbegäran.<br /><br /> När användaren tar bort noder från en pool (genom att ändra storlek på eller krympa poolen) eller när jobbet har inaktiverats, kan användaren ange att aktiviteter som körs på noderna ska köas för körning. Antalet spårar hur många gånger aktiviteten har ställts i kö av dessa orsaker.|
