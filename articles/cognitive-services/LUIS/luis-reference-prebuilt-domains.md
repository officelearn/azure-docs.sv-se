---
title: Fördefinierad domänreferens - LUIS
titleSuffix: Azure Cognitive Services
description: Referens för de fördefinierade domänerna, som är fördefinierade samlingar av avsikter och entiteter från Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 05ad340b3856291832ba0521c7da70ad55260384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270604"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Fördefinierad domänreferens för LUIS-appen
Den här referensen innehåller information om de [fördefinierade domänerna](luis-how-to-use-prebuilt-domains.md), som är fördefinierade samlingar av avsikter och entiteter som LUIS erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), däremot, börja utan avsikter och modeller. Du kan lägga till alla fördefinierade domänavsikter och entiteter i en anpassad modell.

## <a name="custom-domains-per-language"></a>Anpassade domäner per språk

Tabellen nedan sammanfattar de domäner som för närvarande stöds. Stöd för engelska är oftast mer komplett än andra.

| Entitetstyp       | EN-USA      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      Ko |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Kalender  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Kommunikation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-post     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HemAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Anteckningar     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Platser   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurangReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Verktyg      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Väder        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Webb    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Fördefinierade domäner **stöds inte** i:

* Fransk kanadensare
* Hindi
* Spanska mexikanska

## <a name="next-steps"></a>Nästa steg

Lär dig den [enkla entiteten](reference-entity-simple.md).