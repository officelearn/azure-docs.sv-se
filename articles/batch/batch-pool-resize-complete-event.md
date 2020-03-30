---
title: Azure Batch-pool ändra storlek på slutförd händelse
description: Referens för batchpool ändra storlek på slutförd händelse. Se ett exempel på en pool som ökade i storlek och slutfördes.
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022230"
---
# <a name="pool-resize-complete-event"></a>Sluthändelse för storleksändring av pool

 Den här händelsen avges när en poolstorlek har slutförts eller misslyckats.

 I följande exempel visas brödtexten i en pool ändra storlek på slutförd händelse för en pool som ökade i storlek och slutfördes.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|String|Id för poolen.|
|`nodeDeallocationOption`|String|Anger när noder kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **requeue** – Avsluta körande aktiviteter och requeue dem. Aktiviteterna körs igen när jobbet är aktiverat. Ta bort noder så snart aktiviteter har avslutats.<br /><br /> **avsluta** – Avsluta löpaktiviteter. Aktiviteterna kommer inte att köras igen. Ta bort noder så snart aktiviteter har avslutats.<br /><br /> **taskcompletion** – Tillåt att uppgifter som körs för närvarande slutförs. Schemalägg inga nya aktiviteter i väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Lagrade data** - Tillåt att uppgifter som körs slutförs och väntar sedan på att alla lagringsperioder för aktivitetsdata ska upphöra att gälla. Schemalägg inga nya aktiviteter i väntan. Ta bort noder när alla uppgiftskvarhållningsperioder har upphört att gälla.<br /><br /> Standardvärdet är requeue.<br /><br /> Om poolstorleken ökar är värdet inställt på **ogiltigt**.|
|`currentDedicatedNodes`|Int32|Antalet dedikerade beräkningsnoder som för närvarande har tilldelats poolen.|
|`targetDedicatedNodes`|Int32|Antalet dedikerade beräkningsnoder som begärs för poolen.|
|`currentLowPriorityNodes`|Int32|Antalet beräkningsnoder med låg prioritet som för närvarande har tilldelats poolen.|
|`targetLowPriorityNodes`|Int32|Antalet beräkningsnoder med låg prioritet som begärs för poolen.|
|`enableAutoScale`|Bool|Anger om poolstorleken justeras automatiskt med tiden.|
|`isAutoPool`|Bool|Anger om poolen skapades via ett jobbs Automatiska poolmekanism.|
|`startTime`|DateTime|Den tid då poolen ändrar storlek startade.|
|`endTime`|DateTime|Den tid då poolen ändrar storlek har slutförts.|
|`resultCode`|String|Resultatet av storleksstorleken.|
|`resultMessage`|String| Ett detaljerat meddelande om resultatet.<br /><br /> Om storleksstorleken har slutförts anges att åtgärden lyckades.|
