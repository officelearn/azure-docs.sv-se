---
title: Azure Batch pool delete Starthändelse | Microsoft Docs
description: Referens för Batch-pool Starthändelse för borttagning.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774547"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en pool borttagningsåtgärd har startats. Eftersom poolen är en asynkron händelse, kan du förvänta dig en pool Sluthändelse för borttagning som ska skickas när borttagningen är klar.

 I följande exempel visar innehållet i en pool Starthändelse för borttagning.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|id|String|Id för poolen.|

<!-- Update_Description: update metedata properties -->