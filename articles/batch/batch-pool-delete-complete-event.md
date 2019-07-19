---
title: Slutför händelse för borttagning av Azure Batch pool | Microsoft Docs
description: Referens för händelsen borttagning av batch-pool slutförs.
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
ms.openlocfilehash: fd32554866d1e2130fd0833adc1b286fb6bc07a5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323233"
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

|Element|type|Anteckningar|
|-------------|----------|-----------|
|id|Sträng|Poolens ID.|
|startTime|DateTime|Tiden då poolen börjar tas bort.|
|endTime|DateTime|Tiden då poolen togs bort.|

## <a name="remarks"></a>Kommentarer
Mer information om tillstånd och felkoder för att ändra storlek på pooler finns i [ta bort en pool från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).