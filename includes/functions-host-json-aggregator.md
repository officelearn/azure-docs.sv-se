---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279557"
---
Anger hur många funktionsanrop som aggregeras [vid beräkning av mått för Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Egenskap |Default  | Beskrivning |
|---------|---------|---------| 
|batchSize|1000|Maximalt antal begäranden att aggregera.| 
|flushTimeout|00:00:30|Maximal tidsperiod till aggregerad.| 

Funktionstalljoner aggregeras när den första av de två gränserna nås.
