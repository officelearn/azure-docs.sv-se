---
title: "Azure Batch-pool borttagning start-händelsen | Microsoft Docs"
description: "Referens för Batch-pool delete Starta händelsen."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: f8a5241dce422e5c826ab428da6d7bc93284a1cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="pool-delete-start-event"></a>Starta händelsen för poolen borttagning

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