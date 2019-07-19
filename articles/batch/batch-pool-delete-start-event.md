---
title: Start händelse för borttagning av Azure Batch pool | Microsoft Docs
description: Referens för start händelse för borttagning av batch-pool.
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
ms.openlocfilehash: 65d20f2194b2bf83ecf32e19c1ab5b0f7bc7a004
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323220"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en borttagning av en pool har startat. Eftersom poolen ta bort är en asynkron händelse kan du förvänta en borttagnings händelse för poolen som ska genereras när borttagnings åtgärden har slutförts.

 I följande exempel visas innehållet i en start händelse för pool borttagning.

```
{
    "id": "myPool1"
}
```

|Element|type|Anteckningar|
|-------------|----------|-----------|
|id|Sträng|Poolens ID.|