---
title: Starthändelse för storleksändring av Azure Batch-pool | Microsoft Docs
description: Referens för Batch-pool Starthändelse för storleksändring.
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
ms.openlocfilehash: 63abeaca5a9c87945671e79a9c8d7a2512d0d777
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471342"
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

|Element|Type|Anteckningar|
|-------------|----------|-----------|
|poolId|String|Id för poolen.|
|nodeDeallocationOption|String|Anger när noderna kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **ny kö** – avsluta pågående aktiviteter och placeras i kö igen dem. Aktiviteterna körs igen när jobbet har aktiverats. Ta bort noder så fort aktiviteterna har avslutats.<br /><br /> **Avsluta** – avsluta pågående aktiviteter. Aktiviteterna körs inte igen. Ta bort noder så fort aktiviteterna har avslutats.<br /><br /> **taskcompletion** – Tillåt pågående aktiviteter ska slutföras. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** -Låt pågående aktiviteter slutföras och vänta tills alla aktivitetskvarhållningsperioder data upphör att gälla. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla kvarhållningsperioder för aktiviteten har upphört att gälla.<br /><br /> Standardvärdet är ny kö.<br /><br /> Om poolstorleken ökar sedan värdet till **ogiltigt**.|
|currentDedicated|Int32|Antalet beräkningsnoder som för närvarande tilldelat till poolen.|
|targetDedicated|Int32|Antalet beräkningsnoder som har begärts för poolen.|
|enableAutoScale|Bool|Anger om poolstorleken automatiskt justerar över tid.|
|isAutoPool|Bool|Anger om poolen har skapats via ett jobb AutoPool mekanism.|
