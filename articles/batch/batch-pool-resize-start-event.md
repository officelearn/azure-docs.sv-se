---
title: Start händelse för storleks ändring i Azure Batch pool
description: Referens för start händelse för storleks ändring i batch-pool. Exempel visar innehållet i en pool för att ändra storlek på en pool för att ändra storlek på en pool från 0 till 2 noder med en manuell storleks ändring.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 8e2df1c2264392ab79e2dcbb7e58977f341b14d9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026625"
---
# <a name="pool-resize-start-event"></a>Starthändelse för storleksändring av pool

 Den här händelsen genereras när en storleks ändring för poolen har startat. Eftersom storleken på poolen är en asynkron händelse kan du förvänta att en pool för storleks ändring slutförs så att den kan genereras när storleks åtgärden har slutförts.

 I följande exempel visas texten i en pool för att ändra storlek på en pool för att ändra storlek på en pool från 0 till 2 noder med en manuell storleks ändring.

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
|`id`|String|Poolens ID.|
|`nodeDeallocationOption`|String|Anger när noder kan tas bort från poolen, om poolens storlek minskar.<br /><br /> Möjliga värden:<br /><br /> **köa** om – avsluta aktiviteter som körs och köa om dem. Aktiviteterna kommer att köras igen när jobbet är aktiverat. Ta bort noder så snart som aktiviteter har avslut ATS.<br /><br /> **Avsluta** – avsluta aktiviteter som körs. Aktiviteterna kommer inte att köras igen. Ta bort noder så snart som aktiviteter har avslut ATS.<br /><br /> **taskcompletion** – Tillåt att aktiviteter som körs för tillfället slutförs. Schemalägg inga nya aktiviteter under väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** – Tillåt att aktiviteter som körs för tillfället slutförs och vänta sedan på att alla tids perioder för uppgifts data upphör att gälla. Schemalägg inga nya aktiviteter under väntan. Ta bort noder när alla aktiviteter för kvarhållning av aktiviteter har upphört att gälla.<br /><br /> Standardvärdet är Queue.<br /><br /> Om poolens storlek ökar, anges värdet **ogiltigt**.|
|`currentDedicatedNodes`|Int32|Antalet datornoder som för närvarande är kopplade till poolen.|
|`targetDedicatedNodes`|Int32|Antalet Compute-noder som har begärts för poolen.|
|`currentLowPriorityNodes`|Int32|Antalet datornoder som för närvarande är kopplade till poolen.|
|`targetLowPriorityNodes`|Int32|Antalet Compute-noder som har begärts för poolen.|
|`enableAutoScale`|Bool|Anger om poolens storlek justeras automatiskt över tid.|
|`isAutoPool`|Bool|Anger om poolen har skapats via en jobbs mekanism för autopool.|
