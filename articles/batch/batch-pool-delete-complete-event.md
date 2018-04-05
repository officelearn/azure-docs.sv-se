---
title: Azure Batch-pool ta bort händelsen klar | Microsoft Docs
description: Referens för Batch-pool att ta bort händelsen klar.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: bfcbcf40efc64ab1c79ee1a86e02502c68ad6d47
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-complete-event"></a>Sluthändelse för borttagning av pool

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
|startTime|DateTime|Den tid som poolen ta bort startas.|
|endTime|DateTime|Den tid poolen ta bort slutförts.|

## <a name="remarks"></a>Kommentarer
Mer information om tillstånd och felkoder för åtgärden Ändra storlek för poolen finns [ta bort poolen från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).