---
title: Azure Batch-uppgift klar händelse
description: Referens för slutförd batchaktivitet. Den här händelsen avges när en aktivitet har slutförts, oavsett avslutningskoden.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 0fee5f071d5c7005e466bf4c3d0c1d0a7db24731
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022927"
---
# <a name="task-complete-event"></a>Sluthändelse för uppgift

 Den här händelsen avges när en aktivitet har slutförts, oavsett avslutningskoden. Den här händelsen kan användas för att bestämma varaktigheten för en aktivitet, var aktiviteten kördes och om den gjordes på nytt.


 I följande exempel visas brödtexten för en färdig händelse för en aktivitet.

```
{
    "jobId": "myJob",
    "id": "myTask",
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
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`jobId`|String|ID:et för jobbet som innehåller aktiviteten.|
|`id`|String|Uppgiftens ID.|
|`taskType`|String|Typen av aktivitet. Detta kan antingen vara "JobManager" som anger att det är en job manager-uppgift eller "Användare" som anger att det inte är en jobbhanterare. Den här händelsen avges inte för jobbförberedelseaktiviteter, jobbfrigivningsuppgifter eller startaktiviteter.|
|`systemTaskVersion`|Int32|Det här är den interna återförsöksräknaren för en aktivitet. Internt kan batchtjänsten försöka en uppgift igen för att ta hänsyn till tillfälliga problem. Dessa problem kan omfatta interna schemaläggningsfel eller försök att återställa från beräkningsnoder i feltillstånd.|
|[`nodeInfo`](#nodeInfo)|Komplex typ|Innehåller information om beräkningsnoden som aktiviteten kördes på.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplex typ|Anger att aktiviteten är en multi-instance-aktivitet som kräver flera beräkningsnoder.  Se [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) för mer information.|
|[`constraints`](#constraints)|Komplex typ|De körningsvillkor som gäller för den här aktiviteten.|
|[`executionInfo`](#executionInfo)|Komplex typ|Innehåller information om körningen av aktiviteten.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`poolId`|String|ID:t för den pool som aktiviteten kördes på.|
|`nodeId`|String|ID:et för den nod som aktiviteten kördes på.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceSettings

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Antalet beräkningsnoder som krävs av aktiviteten.|

###  <a name="constraints"></a><a name="constraints"></a>Begränsningar

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Det maximala antalet gånger aktiviteten kan göras om. Batch-tjänsten försöker igen en aktivitet om dess avslutningskod inte är noll.<br /><br /> Observera att det här värdet specifikt styr antalet försök. Batch-tjänsten försöker utföra uppgiften en gång och kan sedan försöka igen upp till den här gränsen. Om det maximala antalet försök till exempel är 3 försöker Batch en aktivitet upp till 4 gånger (ett första försök och 3 försök igen).<br /><br /> Om det maximala antalet försök är 0 försök igen försöker inte med aktiviteter.<br /><br /> Om det maximala antalet försök till försök är -1, försöker batch-tjänsten aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga återförsök).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>utförandeInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`startTime`|DateTime|Den tidpunkt då aktiviteten började köras. "Köra" motsvarar **körtillståndet,** så om aktiviteten anger resursfiler eller programpaket, återspeglar starttiden den tid då aktiviteten började hämta eller distribuera dessa.  Om aktiviteten har startats om eller gjorts om är det här den senaste gången aktiviteten började köras.|
|`endTime`|DateTime|Den tidpunkt då aktiviteten slutfördes.|
|`exitCode`|Int32|Uppgiftens avslutningskod.|
|`retryCount`|Int32|Antalet gånger aktiviteten har gjorts om av batch-tjänsten. Aktiviteten görs om den avslutas med en icke-nollavlämningskod, upp till den angivna MaxTaskRetryCount.|
|`requeueCount`|Int32|Antalet gånger aktiviteten har requeued av batch-tjänsten som resultat av en användarbegäran.<br /><br /> När användaren tar bort noder från en pool (genom att ändra storlek på eller krympa poolen) eller när jobbet inaktiveras, kan användaren ange att köra uppgifter på noderna ska tillämpas igen för körning. Det här antalet spårar hur många gånger aktiviteten har återkvisats av dessa skäl.|
