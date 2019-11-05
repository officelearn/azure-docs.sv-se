---
title: Modell version
titleSuffix: Azure Cognitive Services
description: Ange modell versioner i v3-slutpunkterna
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488631"
---
Med version 3 av API för textanalys kan du välja den Textanalys modell som används för dina data. Använd den valfria `model-version` parametern för att välja en version av modellen i dina begär Anden. Om den här parametern inte anges kommer API: et att vara standard `latest`, den senaste stabila modell versionen.

Tillgängliga modell versioner:
* `2019-10-01` (`latest`)

Varje svar från v3-slutpunkterna innehåller ett `model-version`-fält som anger modell versionen som användes.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
