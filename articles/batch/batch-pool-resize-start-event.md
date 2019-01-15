---
title: Starthändelse för storleksändring av Azure Batch-pool | Microsoft Docs
description: Referens för Batch-pool Starthändelse för storleksändring.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 3cabf18cfd771151e62d64dc1d2b47b250ac5471
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258279"
---
# <a name="pool-resize-start-event"></a>Starthändelse för storleksändring av pool

 Den här händelsen genereras när en pool resize har startats. Eftersom poolen storleksändring är en asynkron händelse kan du förvänta dig en pool Sluthändelse för storleksändring som ska skickas när storleksändringen är klar.

 I följande exempel visar innehållet i poolen Starthändelse för storleksändring för en pool storleksändring från 0 till 2 noder med en manuell storleksändring.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|poolId|Sträng|Id för poolen.|
|nodeDeallocationOption|Sträng|Anger när noderna kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **ny kö** – avsluta pågående aktiviteter och placeras i kö igen dem. Aktiviteterna körs igen när jobbet har aktiverats. Ta bort noder så fort aktiviteterna har avslutats.<br /><br /> **Avsluta** – avsluta pågående aktiviteter. Aktiviteterna körs inte igen. Ta bort noder så fort aktiviteterna har avslutats.<br /><br /> **taskcompletion** – Tillåt pågående aktiviteter ska slutföras. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** -Låt pågående aktiviteter slutföras och vänta tills alla aktivitetskvarhållningsperioder data upphör att gälla. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla kvarhållningsperioder för aktiviteten har upphört att gälla.<br /><br /> Standardvärdet är ny kö.<br /><br /> Om poolstorleken ökar sedan värdet till **ogiltigt**.|
|currentDedicated|Int32|Antalet beräkningsnoder som för närvarande tilldelat till poolen.|
|targetDedicated|Int32|Antalet beräkningsnoder som har begärts för poolen.|
|enableAutoScale|Bool|Anger om poolstorleken automatiskt justerar över tid.|
|isAutoPool|Bool|Anger om poolen har skapats via ett jobb AutoPool mekanism.|
