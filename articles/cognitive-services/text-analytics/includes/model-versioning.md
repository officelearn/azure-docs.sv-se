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
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021193"
---
Med version 3 av API för textanalys kan du välja den Textanalys modell som används för dina data. Använd den valfria `model-version` parametern för att välja en version av modellen i dina begär Anden. Om den här parametern inte anges kommer API: et att vara standard `latest`, den senaste stabila modell versionen.

Tillgängliga modell versioner:
* `2019-10-01` (`latest`)

Varje svar från v3-slutpunkterna innehåller ett `model-version`-fält som anger modell versionen som användes.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
