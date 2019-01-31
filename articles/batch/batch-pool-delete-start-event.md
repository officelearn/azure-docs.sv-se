---
title: Azure Batch pool delete Starthändelse | Microsoft Docs
description: Referens för Batch-pool Starthändelse för borttagning.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474300"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en pool borttagningsåtgärd har startats. Eftersom poolen är en asynkron händelse, kan du förvänta dig en pool Sluthändelse för borttagning som ska skickas när borttagningen är klar.

 I följande exempel visar innehållet i en pool Starthändelse för borttagning.

```
{
    "id": "myPool1"
}
```

|Element|Type|Anteckningar|
|-------------|----------|-----------|
|id|String|Id för poolen.|