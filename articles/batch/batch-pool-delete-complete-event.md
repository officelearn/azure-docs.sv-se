---
title: "Azure Batch-pool ta bort händelsen klar | Microsoft Docs"
description: "Referens för Batch-pool att ta bort händelsen klar."
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
ms.openlocfilehash: 890f2ba7fda37060c56177868d6214d517d91831
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="pool-delete-complete-event"></a>Händelsen i poolen ta bort klar

 Denna händelse genereras när en programpool borttagningen har slutförts.

 I följande exempel visar innehållet i en pool delete complete-händelse.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|id|Sträng|Id för poolen.|
|startTime|Datum och tid|Den tid som poolen ta bort startas.|
|endTime|Datum och tid|Den tid poolen ta bort slutförts.|

## <a name="remarks"></a>Kommentarer
Mer information om tillstånd och felkoder för åtgärden Ändra storlek för poolen finns [ta bort poolen från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).