---
title: Azure Batch pool borttagning komplett händelse
description: Referens för batchpool borttagning fullständig händelse. Den här händelsen avges när en poolborttagningsåtgärd har slutförts.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023624"
---
# <a name="pool-delete-complete-event"></a>Sluthändelse för borttagning av pool

 Den här händelsen avges när en poolborttagningsåtgärd har slutförts.

 I följande exempel visas brödtexten för en poolborttagning fullständig händelse.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|String|Id för poolen.|
|`startTime`|DateTime|Den tid som poolen tas bort startade.|
|`endTime`|DateTime|Den tidpunkt då poolen tas bort slutfördes.|

## <a name="remarks"></a>Anmärkningar
Mer information om tillstånd och felkoder för poolstorleksåtgärd finns i [Ta bort en pool från ett konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
