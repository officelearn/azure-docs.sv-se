---
title: Slutför händelse för borttagning av Azure Batch pool
description: Referens för händelsen borttagning av batch-pool slutförs. Den här händelsen genereras när en borttagning av en pool har slutförts.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d28223c79f96f35a6ee11f98e0f09f21d7db4451
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115949"
---
# <a name="pool-delete-complete-event"></a>Sluthändelse för borttagning av pool

 Den här händelsen genereras när en borttagning av en pool har slutförts.

 I följande exempel visas innehållet i en slutförd händelse för borttagning av pooler.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
|`startTime`|DateTime|Tiden då poolen börjar tas bort.|
|`endTime`|DateTime|Tiden då poolen togs bort.|

## <a name="remarks"></a>Anmärkningar
Mer information om tillstånd och felkoder för att ändra storlek på pooler finns i [ta bort en pool från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
