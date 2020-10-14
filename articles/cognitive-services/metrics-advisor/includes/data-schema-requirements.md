---
title: Krav för data schema
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: c754efef02cdad6edbf047c5de9f1af6d758f137
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043226"
---
Mått övervakaren är en tjänst för avvikelse identifiering, diagnostik och analys av tids serier. Som en AI-driven tjänst använder den dina data för att träna modellen som används. Tjänsten accepterar tabeller med sammanställda data med följande kolumner:

* **Mått** (obligatoriskt): en eller flera kolumner som innehåller numeriska värden.
* **Timestamp** (valfritt): noll eller en kolumn med typen `DateTime` eller `String` . När den här kolumnen inte anges anges tidsstämpeln som start tid för varje inmatnings period. Formatera tidsstämpeln i: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Din tidstämpel ska matcha måttets granularitet. Ett dagligt mått bör till exempel se till att timme, minut och sekund på tidsstämpeln är `00:00:00` märkta som **.
* **Dimension** (valfritt): kolumner kan vara av vilken datatyp som helst. Var försiktig när du arbetar med stora volymer med kolumner och värden för att förhindra att stora mängder dimensioner bearbetas.

> [!Note]
> För varje mått bör det bara finnas en tidsstämpel per mått, motsvarande en dimensions kombination. Sammanställ dina data före onboarding eller Använd frågan för att ange de data som ska matas in.