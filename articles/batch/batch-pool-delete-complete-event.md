---
title: Azure Batch-pool ta bort Sluthändelse | Microsoft Docs
description: Referens för Batch-pool ta bort händelsen klar.
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
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474556"
---
# <a name="pool-delete-complete-event"></a>Sluthändelse för borttagning av pool

 Den här händelsen genereras när en pool delete-åtgärd har slutförts.

 I följande exempel visar innehållet i en pool delete fullständig händelse.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Type|Anteckningar|
|-------------|----------|-----------|
|id|String|Id för poolen.|
|startTime|DateTime|Den tid som poolen ta bort igång.|
|endTime|DateTime|Den tid ta bort poolen har slutförts.|

## <a name="remarks"></a>Kommentarer
Läs mer om tillstånd och felkoder för poolen storleksändringen [ta bort en pool från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).