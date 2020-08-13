---
title: Fördefinierad domän referens – LUIS
titleSuffix: Azure Cognitive Services
description: Referens för fördefinierade domäner, som är färdiga samlingar av avsikter och entiteter från Language Understanding intelligenta tjänster (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: d41ec3abfa2e562d5a6e11dd9ed0fcbcffead5c6
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191974"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Fördefinierad domän referens för din LUIS-app
Den här referensen innehåller information om [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md), som är färdiga samlingar av intentor och entiteter som Luis erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), med kontrast, börjar utan avsikter och modeller. Du kan lägga till eventuella fördefinierade domän intentor och entiteter i en anpassad modell.

## <a name="prebuilt-domains-per-language"></a>Fördefinierade domäner per språk

Tabellen nedan sammanfattar de domäner som stöds för närvarande. Stöd för engelska är vanligt vis mer komplett än andra.

| Entitetstyp       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Kalender  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Kommunikation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-post     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Kommentarer     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Placerar   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Att göra     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Verktyg      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Vädret        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Webb    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Fördefinierade domäner **stöds inte** i:

* Franska, kanadensisk
* Hindi
* Spanska mexikanska

## <a name="next-steps"></a>Nästa steg

Lär dig den [enkla entiteten](reference-entity-simple.md).
