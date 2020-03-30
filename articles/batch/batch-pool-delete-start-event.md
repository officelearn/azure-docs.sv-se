---
title: Starthändelse för borttagning av Azure Batch-pool
description: Referens för starthändelse för batchpool. Den här händelsen avges när en poolborttagningsåtgärd har startat.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022213"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen avges när en poolborttagningsåtgärd har startat. Eftersom borttagning av poolen är en asynkron händelse kan du förvänta dig att en poolborttagning fullständig händelse ska skickas ut när borttagningsåtgärden är klar.

 I följande exempel visas brödtexten för en starthändelse för poolborttagning.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|String|Id för poolen.|
