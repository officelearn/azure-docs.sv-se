---
title: Azure Batch-pool ändra storlek på händelsen klar | Microsoft Docs
description: Referens för Batch-pool ändra storlek på händelsen klar.
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
ms.openlocfilehash: e91ba664a69d28cae1f82710d427bd2a391305a2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="pool-resize-complete-event"></a>Sluthändelse för storleksändring av pool

 Denna händelse genereras när en storlek för poolen har slutförts eller misslyckades.

 I följande exempel visar innehållet i en pool fullständig storleksändringar för en pool som ökar i storlek och har slutförts.

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
|id|Sträng|Id för poolen.|
|nodeDeallocationOption|Sträng|Anger om noder kan tas bort från poolen, om poolstorleken minskar.<br /><br /> Möjliga värden:<br /><br /> **meddelanden** – avsluta pågående aktiviteter och ställ dem i kö. Aktiviteterna körs igen när jobbet har aktiverats. Ta bort noder när aktiviteterna har avslutats.<br /><br /> **Avsluta** – avsluta pågående aktiviteter. Uppgifterna kan inte köras igen. Ta bort noder när aktiviteterna har avslutats.<br /><br /> **taskcompletion** – Tillåt pågående aktiviteter för att slutföra. Schemalägg inga nya aktiviteter väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** -Låt pågående aktiviteter slutföras och vänta i alla aktivitetskvarhållningsperioder data upphör att gälla. Schemalägg inga nya aktiviteter väntan. Ta bort noder när kvarhållningsperioder för alla aktivitet har upphört att gälla.<br /><br /> Standardvärdet är meddelanden.<br /><br /> Om poolstorleken ökar sedan värdet till **ogiltigt**.|
|currentDedicated|Int32|Antal compute-noder för närvarande tilldelat till poolen.|
|targetDedicated|Int32|Antal compute-noder som har begärts för poolen.|
|enableAutoScale|Booleskt|Anger om justerar poolstorleken automatiskt med tiden.|
|isAutoPool|Booleskt|Anger om poolen har skapats via ett jobb AutoPool mekanism.|
|startTime|DateTime|Ändra storlek på poolen tiden har startats.|
|endTime|DateTime|Den tid ändra storlek på poolen slutförts.|
|ResultCode|Sträng|Resultatet av storlek.|
|resultMessage|Sträng|Ändra storlek på fel finns information om resultatet.<br /><br /> Om storleksändringen har slutförts det anger att åtgärden lyckades.|