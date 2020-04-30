---
title: Start händelse för borttagning av Azure Batch pool
description: Referens för start händelse för borttagning av batch-pool. Den här händelsen genereras när en borttagning av en pool har startat.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 38e419e549006d3fde2f1694e0d40e620cd438e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115932"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en borttagning av en pool har startat. Eftersom poolen ta bort är en asynkron händelse kan du förvänta en borttagnings händelse för poolen som ska genereras när borttagnings åtgärden har slutförts.

 I följande exempel visas innehållet i en start händelse för pool borttagning.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Obs!|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
