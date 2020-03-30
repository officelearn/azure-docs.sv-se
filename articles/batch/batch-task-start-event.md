---
title: Starthändelse för Azure Batch-aktivitet
description: Referensinformation för batchaktivitetsstarthändelsen. Den här händelsen genereras när en aktivitet har schemalagts för att starta på en beräkningsnod av schemaläggaren.
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
ms.openlocfilehash: bed3749e29867298f3e8258a08448b7b094055ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022825"
---
# <a name="task-start-event"></a>Starthändelse för uppgift

 Den här händelsen genereras när en aktivitet har schemalagts för att starta på en beräkningsnod av schemaläggaren. Observera att om aktiviteten görs om eller omräknas kommer den här händelsen att skickas ut igen för samma aktivitet, men antalet försök och systemuppgiftsversionen uppdateras i enlighet med detta.


 I följande exempel visas brödtexten för en aktivitetsstarthändelse.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`jobId`|String|ID:et för jobbet som innehåller aktiviteten.|
|`id`|String|Uppgiftens ID.|
|`taskType`|String|Typen av aktivitet. Detta kan antingen vara "JobManager" som anger att det är en job manager-uppgift eller "Användare" som anger att det inte är en jobbhanterare.|
|`systemTaskVersion`|Int32|Det här är den interna återförsöksräknaren för en aktivitet. Internt kan batchtjänsten försöka en uppgift igen för att ta hänsyn till tillfälliga problem. Dessa problem kan omfatta interna schemaläggningsfel eller försök att återställa från beräkningsnoder i feltillstånd.|
|[`nodeInfo`](#nodeInfo)|Komplex typ|Innehåller information om beräkningsnoden som aktiviteten kördes på.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplex typ|Anger att aktiviteten är multi-instance-aktivitet som kräver flera beräkningsnoder.  Mer information finns i [multiInstanceSettings.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task)|
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
|`numberOfInstances`|Int|Antalet beräkningsnoder som krävs av aktiviteten.|

###  <a name="constraints"></a><a name="constraints"></a>Begränsningar

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Det maximala antalet gånger aktiviteten kan göras om. Batch-tjänsten försöker igen en aktivitet om dess avslutningskod inte är noll.<br /><br /> Observera att det här värdet specifikt styr antalet försök. Batch-tjänsten försöker utföra uppgiften en gång och kan sedan försöka igen upp till den här gränsen. Om det maximala antalet försök till exempel är 3 försöker Batch en aktivitet upp till 4 gånger (ett första försök och 3 försök igen).<br /><br /> Om det maximala antalet försök är 0 försök igen försöker inte med aktiviteter.<br /><br /> Om det maximala antalet försök till försök är -1, försöker batch-tjänsten aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga återförsök).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>utförandeInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`retryCount`|Int32|Antalet gånger aktiviteten har gjorts om av batch-tjänsten. Aktiviteten görs om den avslutas med en icke-nollutträdeskod, upp till den angivna MaxTaskRetryCount|
