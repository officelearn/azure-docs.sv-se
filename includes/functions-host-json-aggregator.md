---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: da0cbab59d9c801419ac4b3704fee3275f337fd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131630"
---
Anger hur många funktionsanrop är aggregeras när [beräkning av mätvärden för Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Egenskap |Standard  | Beskrivning |
|---------|---------|---------| 
|batchSize|1000|Maximalt antal begäranden ska aggregeras.| 
|flushTimeout|00:00:30|Maximal tid period ska aggregeras.| 

Funktionsanrop räknas samman när först av två begränsar uppnås.