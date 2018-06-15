---
title: Azure Batch-pool storleksändring Starta händelsen | Microsoft Docs
description: Referens för Batch-pool storleksändringar start.
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
ms.openlocfilehash: b4412764c313669dc154fccaa56f22417262994d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312617"
---
# <a name="pool-resize-start-event"></a>Starthändelse för storleksändring av pool

 Denna händelse genereras när en storlek för poolen har startats. Eftersom storleksändringen av poolen är en asynkron händelse, du kan förvänta dig en pool fullständig storleksändringar som ska skickas när åtgärden Ändra storlek har slutförts.

 I följande exempel visar innehållet i en pool start storleksändringar för en pool storleksändring från 0 till 2 noder med en manuell.

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
|nodeDeallocationOption|Sträng|Anger om noder kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **meddelanden** – avsluta pågående aktiviteter och ställ dem i kö. Aktiviteterna körs igen när jobbet har aktiverats. Ta bort noder när aktiviteterna har avslutats.<br /><br /> **Avsluta** – avsluta pågående aktiviteter. Uppgifterna kan inte köras igen. Ta bort noder när aktiviteterna har avslutats.<br /><br /> **taskcompletion** – Tillåt pågående aktiviteter för att slutföra. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** -Låt pågående aktiviteter slutföras och vänta i alla aktivitetskvarhållningsperioder data upphör att gälla. Schemalägg inga nya aktiviteter väntan. Ta bort noder när kvarhållningsperioder för alla aktivitet har upphört att gälla.<br /><br /> Standardvärdet är meddelanden.<br /><br /> Om poolstorleken ökar sedan värdet till **ogiltigt**.|
|currentDedicated|Int32|Antal compute-noder för närvarande tilldelat till poolen.|
|targetDedicated|Int32|Antal compute-noder som har begärts för poolen.|
|enableAutoScale|Booleskt|Anger om justerar poolstorleken automatiskt med tiden.|
|isAutoPool|Booleskt|Speficies om poolen har skapats via ett jobb AutoPool mekanism.|
