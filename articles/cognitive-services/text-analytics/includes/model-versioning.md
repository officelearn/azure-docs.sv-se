---
title: Versionshantering av modell
titleSuffix: Azure Cognitive Services
description: Ange modellversioner i V3-slutpunkterna
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77089025"
---
Version 3 av Text Analytics API kan du välja den modellversion som är mest aktuell för dina data. Använd den `model-version` valfria parametern för att välja den version av modellen som önskas för dina begäranden. Om den här parametern inte anges `latest`kommer API:et som standard att användas i den senaste stabila versionen. Även om du kan använda den senaste modellversionen i alla begäranden uppdateras endast vissa funktioner i varje version. I tabellen nedan beskrivs vilka funktioner som har uppdaterats i varje modellversion:

| Modellversion           | Funktioner uppdaterade         | Senaste versionen för:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Enhetsidentifiering                      | Enhetsidentifiering                      |
| `2019-10-01`            | Erkännande av entitet, Sentimentanalys  | Språkidentifiering, extraktion av nyckelfraser, Sentimentanalys|


Varje svar från v3-slutpunkterna innehåller ett `model-version` fält som anger modellversionen som användes.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Se [Nyheter](../whats-new.md) för information om uppdateringarna för dessa modellversioner.
