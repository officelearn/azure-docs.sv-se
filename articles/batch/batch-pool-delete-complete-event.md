---
title: Slutför händelse för borttagning av Azure Batch pool
description: Referens för händelsen borttagning av batch-pool slutförs. Den här händelsen genereras när en borttagning av en pool har slutförts.
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
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023624"
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
|`id`|String|Poolens ID.|
|`startTime`|DateTime|Tiden då poolen börjar tas bort.|
|`endTime`|DateTime|Tiden då poolen togs bort.|

## <a name="remarks"></a>Anmärkningar
Mer information om tillstånd och felkoder för att ändra storlek på pooler finns i [ta bort en pool från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
