---
title: "Azure Batch aktiviteten Starta händelsen | Microsoft Docs"
description: "Referens för batchaktiviteten Starta händelsen."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: c47ab36c99dddd46a14c15018a2a46bf7f873ffa
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="task-start-event"></a>Aktiviteten Starta händelsen

 Denna händelse genereras när en aktivitet har schemalagts att starta på en beräkningsnod av Schemaläggaren. Observera att om aktiviteten är ett nytt försök eller begärdes kommer orsakat den här händelsen igen för samma aktivitet, men antal försök och systemversionen aktivitet uppdateras.


 I följande exempel visar innehållet i en aktivitet starta en händelse.

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

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|JobId|Sträng|Id för jobbet som innehåller aktiviteten.|
|id|Sträng|Id för aktiviteten.|
|taskType|Sträng|Typ av uppgiften. Detta kan vara visar det är en projektaktivitet manager ' JobManager' eller 'User' som anger den inte är en projektaktivitet manager.|
|systemTaskVersion|Int32|Detta är räknaren interna försök för en uppgift. Internt kan Batch-tjänsten försöka med att en aktivitet för att tillfälligt problem. De här problemen kan innehålla interna schemaläggning fel eller försöker återställa från compute-noder i ett felaktigt tillstånd.|
|[nodeInfo](#nodeInfo)|Komplex typ|Innehåller information om Beräkningsnoden där aktiviteten kördes.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex typ|Anger att det är flera Instansuppgift som kräver flera compute-noder.  Se [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) mer information.|
|[villkor](#constraints)|Komplex typ|Begränsningar för körning som gäller för den här uppgiften.|
|[executionInfo](#executionInfo)|Komplex typ|Innehåller information om körning av uppgiften.|

###  <a name="nodeInfo"></a>nodeInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|poolId|Sträng|Id för poolen som aktiviteten kördes.|
|nodeId|Sträng|Id för noden som aktiviteten kördes.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|numberOfInstances|int|Antalet beräkningsnoder som uppgiften kräver.|

###  <a name="constraints"></a>villkor

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maximalt antal gånger uppgiften får köras på nytt. Batch-tjänsten försöker upprätta en uppgift om dess slutkod inte är noll.<br /><br /> Observera att det här värdet uttryckligen styr antalet försök. Batch-tjänsten försöker aktiviteten en gång och försök sedan upp till den här gränsen. Till exempel maximalt antal försök är 3, Batch försöker en aktivitet upp till gånger 4 (ett första försök och 3 försök).<br /><br /> Maximalt antal försök är 0, försök uppgifter inte igen av Batch-tjänsten.<br /><br /> Om det högsta antalet nya försök är -1, försöker Batch-tjänsten aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga nya försök).|

###  <a name="executionInfo"></a>executionInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|retryCount|Int32|Antal gånger som aktiviteten gjordes av Batch-tjänsten. Uppgiften försöks om avslutas med ett annat värde än noll slutkoden upp till den angivna MaxTaskRetryCount|
