---
title: Modell version
titleSuffix: Azure Cognitive Services
description: Ange modell versioner i v3-slutpunkterna
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77089025"
---
Med version 3 av API för textanalys kan du välja den modell version som är mest aktuell för dina data. Använd den valfria `model-version` parametern för att välja den version av modellen som önskas för dina begär Anden. Om den här parametern inte anges kommer API: et `latest`att standardvärdet vara den senaste stabila versionen. Även om du kan använda den senaste modell versionen i en begäran, uppdateras bara vissa funktioner i varje version. I tabellen nedan beskrivs vilka funktioner som har uppdaterats i varje modell version:

| Modell version           | Uppdaterade funktioner         | Senaste version för:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Enhetsidentifiering                      | Enhetsidentifiering                      |
| `2019-10-01`            | Enhets igenkänning, sentiment analys  | Språk identifiering, extrahering av nyckel fraser, sentiment analys|


Varje svar från v3-slutpunkterna innehåller `model-version` ett fält som anger modell versionen som användes.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Se [vad som är nytt](../whats-new.md) för information om uppdateringarna för dessa modell versioner.
