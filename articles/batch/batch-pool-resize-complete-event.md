---
title: Azure Batch-pool ändra storlek på Sluthändelse | Microsoft Docs
description: Referens för Batch-pool ändra storlek på händelsen klar.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776430"
---
# <a name="pool-resize-complete-event"></a>Sluthändelse för storleksändring av pool

 Den här händelsen genereras när en pool resize har slutförts eller misslyckats.

 I följande exempel visar innehållet i en pool Sluthändelse för storleksändring för en pool som ökar i storlek och har slutförts.

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

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|id|String|Id för poolen.|
|nodeDeallocationOption|String|Anger när noderna kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **ny kö** – avsluta pågående aktiviteter och placeras i kö igen dem. Aktiviteterna körs igen när jobbet har aktiverats. Ta bort noder så fort aktiviteterna har avslutats.<br /><br /> **Avsluta** – avsluta pågående aktiviteter. Aktiviteterna körs inte igen. Ta bort noder så fort aktiviteterna har avslutats.<br /><br /> **taskcompletion** – Tillåt pågående aktiviteter ska slutföras. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** -Låt pågående aktiviteter slutföras och vänta tills alla aktivitetskvarhållningsperioder data upphör att gälla. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla kvarhållningsperioder för aktiviteten har upphört att gälla.<br /><br /> Standardvärdet är ny kö.<br /><br /> Om poolstorleken ökar sedan värdet till **ogiltigt**.|
|currentDedicated|Int32|Antalet beräkningsnoder som för närvarande tilldelat till poolen.|
|targetDedicated|Int32|Antalet beräkningsnoder som har begärts för poolen.|
|enableAutoScale|Booleskt|Anger om poolstorleken automatiskt justerar över tid.|
|isAutoPool|Booleskt|Anger om poolen har skapats via ett jobb AutoPool mekanism.|
|startTime|DateTime|Den tid som ändrar storlek på poolen igång.|
|endTime|DateTime|Den tid ändrar storlek på poolen har slutförts.|
|resultCode|String|Resultatet av den ändrade storleken.|
|resultMessage|String|Storleksändringsfel returneras innehåller information om resultatet.<br /><br /> Om den ändrade storleken har slutförts det tillstånd som åtgärden har slutförts.|