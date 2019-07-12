---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4d0a61bf7f4ee9e441a49e21ce6535dc3bff0edc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608384"
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