---
title: Start händelse för borttagning av Azure Batch pool
description: Referens för start händelse för borttagning av batch-pool. Den här händelsen genereras när en borttagning av en pool har startat.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723943"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en borttagning av en pool har startat. Eftersom poolen ta bort är en asynkron händelse kan du förvänta en borttagnings händelse för poolen som ska genereras när borttagnings åtgärden har slutförts.

 I följande exempel visas innehållet i en start händelse för pool borttagning.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
