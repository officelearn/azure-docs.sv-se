---
title: "Azure Batch misslyckas händelseuppgiften | Microsoft Docs"
description: "Referens för batchaktiviteten misslyckas händelse."
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
ms.openlocfilehash: 08feb4ec34bb1635f8ea744b54a10b677b94ab3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="task-fail-event"></a>Händelse för aktiviteten misslyckas

 Denna händelse genereras när en aktivitet har slutförts med ett fel. Alla avslutningskoder som inte är noll är för närvarande anses vara fel. Den här händelsen att orsakat *förutom* en uppgift Slutför händelse och kan användas för att identifiera när en aktivitet misslyckades.


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
|JobId|Sträng|Id för jobbet som innehåller aktiviteten.|
|id|Sträng|Id för aktiviteten.|
|taskType|Sträng|Typ av uppgiften. Detta kan vara visar det är en projektaktivitet manager ' JobManager' eller 'User' som anger den inte är en projektaktivitet manager. Den här händelsen har inte genererats för jobbet förberedande uppgifter, versionen jobbuppgifter eller starta uppgifter.|
|systemTaskVersion|Int32|Detta är räknaren interna försök för en uppgift. Internt kan Batch-tjänsten försöka med att en aktivitet för att tillfälligt problem. De här problemen kan innehålla interna schemaläggning fel eller försöker återställa från compute-noder i ett felaktigt tillstånd.|
|[nodeInfo](#nodeInfo)|Komplex typ|Innehåller information om Beräkningsnoden där aktiviteten kördes.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex typ|Anger att aktiviteten är en aktivitet för flera instanser som kräver flera compute-noder.  Se [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) mer information.|
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
|numberOfInstances|Int32|Antalet beräkningsnoder som uppgiften kräver.|

###  <a name="constraints"></a>villkor

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maximalt antal gånger uppgiften får köras på nytt. Batch-tjänsten försöker upprätta en uppgift om dess slutkod inte är noll.<br /><br /> Observera att det här värdet uttryckligen styr antalet försök. Batch-tjänsten försöker aktiviteten en gång och försök sedan upp till den här gränsen. Till exempel maximalt antal försök är 3, Batch försöker en aktivitet upp till gånger 4 (ett första försök och 3 försök).<br /><br /> Maximalt antal försök är 0, försök uppgifter inte igen av Batch-tjänsten.<br /><br /> Om det högsta antalet nya försök är -1, försöker Batch-tjänsten aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga nya försök).|


###  <a name="executionInfo"></a>executionInfo

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|startTime|Datum och tid|Den tid då aktiviteten startades. 'Körs' motsvarar den **kör** tillstånd, så om aktiviteten anger resursfiler eller programpaket, visar starttiden tidpunkten då aktiviteten startades hämtas eller distribution av dessa.  Om aktiviteten har startats om eller ett nytt försök, är det senaste tillfälle då aktiviteten startades.|
|endTime|Datum och tid|Den tid då aktiviteten är slutförd.|
|Slutkod|Int32|Slutkoden för aktiviteten.|
|retryCount|Int32|Antal gånger som aktiviteten gjordes av Batch-tjänsten. Uppgiften försöks om avslutas med ett annat värde än noll slutkoden upp till den angivna MaxTaskRetryCount.|
|requeueCount|Int32|Antalet gånger uppgiften har åter placerats i kö av Batch-tjänsten som ett resultat av en användarbegäran.<br /><br /> När användaren tar bort noder från en pool (genom att ändra storlek på eller minska storleken på poolen) eller när jobbet är inaktiverad, användaren kan ange att aktiviteter som körs på noderna vara placerats i kö för körning. Räknaren håller reda på hur många gånger uppgiften har åter placerats i kö av dessa skäl.|
