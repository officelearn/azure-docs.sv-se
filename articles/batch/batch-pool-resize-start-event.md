---
title: Azure Batch-pool ändra storlek på starthändelsen
description: Referens för batchpool ändra storlek på starthändelsen. Exempel visar brödtexten i en pool ändra storlek starthändelse för en pool storleksändring från 0 till 2 noder med en manuell storlekstorlek.
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
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023590"
---
# <a name="pool-resize-start-event"></a>Starthändelse för storleksändring av pool

 Den här händelsen avges när en poolstorlek har startat. Eftersom poolen storleksstorlek är en asynkron händelse, kan du förvänta dig en pool ändra storlek fullständig händelse som ska avges när storleksstorleken åtgärden slutförs.

 I följande exempel visas brödtexten i en pool ändra storlek på starthändelsen för en poolstorlek från 0 till 2 noder med en manuell storleksändring.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|String|Id för poolen.|
|`nodeDeallocationOption`|String|Anger när noder kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **requeue** – Avsluta körande aktiviteter och requeue dem. Aktiviteterna körs igen när jobbet är aktiverat. Ta bort noder så snart aktiviteter har avslutats.<br /><br /> **avsluta** – Avsluta löpaktiviteter. Aktiviteterna kommer inte att köras igen. Ta bort noder så snart aktiviteter har avslutats.<br /><br /> **taskcompletion** – Tillåt att uppgifter som körs för närvarande slutförs. Schemalägg inga nya aktiviteter i väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Lagrade data** - Tillåt att uppgifter som körs slutförs och väntar sedan på att alla lagringsperioder för aktivitetsdata ska upphöra att gälla. Schemalägg inga nya aktiviteter i väntan. Ta bort noder när alla uppgiftskvarhållningsperioder har upphört att gälla.<br /><br /> Standardvärdet är requeue.<br /><br /> Om poolstorleken ökar är värdet inställt på **ogiltigt**.|
|`currentDedicatedNodes`|Int32|Antalet beräkningsnoder som för närvarande har tilldelats poolen.|
|`targetDedicatedNodes`|Int32|Antalet beräkningsnoder som begärs för poolen.|
|`currentLowPriorityNodes`|Int32|Antalet beräkningsnoder som för närvarande har tilldelats poolen.|
|`targetLowPriorityNodes`|Int32|Antalet beräkningsnoder som begärs för poolen.|
|`enableAutoScale`|Bool|Anger om poolstorleken justeras automatiskt med tiden.|
|`isAutoPool`|Bool|Anger om poolen skapades via ett jobbs Automatiska poolmekanism.|
