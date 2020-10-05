---
title: Krav för data schema
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 33bc52d3c334919a9e93d9666a24d85e3fe158b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376593"
---
Mått övervakaren är en tjänst för avvikelse identifiering, diagnostik och analys av tids serier. Som en AI-driven tjänst använder den dina data för att träna modellen som används. Tjänsten accepterar tabeller med sammanställda data med följande kolumner:

* **Mått** (obligatoriskt): en eller flera kolumner som innehåller numeriska värden.
* **Timestamp** (valfritt): noll eller en kolumn med typen `DateTime` eller `String` . När den här kolumnen inte anges anges tidsstämpeln som start tid för varje inmatnings period. Formatera tidsstämpeln i: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Din tidstämpel ska matcha måttets granularitet. Ett dagligt mått bör till exempel se till att timme, minut och sekund på tidsstämpeln är `00:00:00` märkta som **.
* **Dimension** (valfritt): kolumner kan vara av vilken datatyp som helst. Var försiktig när du arbetar med stora volymer med kolumner och värden för att förhindra att stora mängder dimensioner bearbetas.

> [!Note]
> För varje mått bör det bara finnas en tidsstämpel per mått, motsvarande en dimensions kombination. Sammanställ dina data före onboarding eller Använd frågan för att ange de data som ska matas in.