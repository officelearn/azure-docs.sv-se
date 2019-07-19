---
title: Slutför händelse för storleks ändring av Azure Batch pool | Microsoft Docs
description: Referens för slutförd händelse för storleks ändring i batch-pool.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c2544bd2be683b731c3dac0bea651d4b64dff75e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323190"
---
# <a name="pool-resize-complete-event"></a>Sluthändelse för storleksändring av pool

 Den här händelsen genereras när en storleks ändring för poolen har slutförts eller misslyckats.

 I följande exempel visas innehållet i en storleks ändring i en pool för en pool som har ökat i storlek och slutförts.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Element|type|Anteckningar|
|-------------|----------|-----------|
|id|Sträng|Poolens ID.|
|nodeDeallocationOption|Sträng|Anger när noder kan tas bort från poolen, om poolens storlek minskar.<br /><br /> Möjliga värden är:<br /><br /> **köa** om – avsluta aktiviteter som körs och köa om dem. Aktiviteterna kommer att köras igen när jobbet är aktiverat. Ta bort noder så snart som aktiviteter har avslut ATS.<br /><br /> **Avsluta** – avsluta aktiviteter som körs. Aktiviteterna kommer inte att köras igen. Ta bort noder så snart som aktiviteter har avslut ATS.<br /><br /> **taskcompletion** – Tillåt att aktiviteter som körs för tillfället slutförs. Schemalägg inga nya aktiviteter under väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** – Tillåt att aktiviteter som körs för tillfället slutförs och vänta sedan på att alla tids perioder för uppgifts data upphör att gälla. Schemalägg inga nya aktiviteter under väntan. Ta bort noder när alla aktiviteter för kvarhållning av aktiviteter har upphört att gälla.<br /><br /> Standardvärdet är Queue.<br /><br /> Om poolens storlek ökar, anges värdet **ogiltigt**.|
|currentDedicated|Int32|Antalet datornoder som för närvarande är kopplade till poolen.|
|targetDedicated|Int32|Antalet Compute-noder som har begärts för poolen.|
|enableAutoScale|Bool|Anger om poolens storlek justeras automatiskt över tid.|
|isAutoPool|Bool|Anger om poolen har skapats via en jobbs mekanism för autopool.|
|startTime|DateTime|Tiden då storleken på poolen startades.|
|endTime|DateTime|Tiden då storleken på poolen har ändrats.|
|resultCode|Sträng|Resultatet av storleks ändring.|
|resultMessage|Sträng|Fel meddelandet ändra storlek innehåller information om resultatet.<br /><br /> Om storleken har slutförts anger det att åtgärden lyckades.|