---
title: Azure Batch-pool borttagning start-händelsen | Microsoft Docs
description: Referens för Batch-pool delete Starta händelsen.
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
ms.openlocfilehash: 8737b9ff6452730ff5a55fa7324e37f0fe715433
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Denna händelse genereras när en programpool borttagningsåtgärd har startats. Eftersom poolen är en asynkron händelse, du kan förvänta dig en pool ta bort händelsen klar som ska skickas när borttagningen är klar.

 I följande exempel visar innehållet i en pool borttagning start-händelse.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|id|Sträng|Id för poolen.|