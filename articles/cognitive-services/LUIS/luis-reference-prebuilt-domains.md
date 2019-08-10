---
title: Fördefinierad domän referens – LUIS
titleSuffix: Azure Cognitive Services
description: Referens för de fördefinierade domäner som är färdiga samlingar av avsikter och entiteter från Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6e701396191ec8bf66ece0e2858b9f32857e7b17
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933593"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Fördefinierad domän referens för din LUIS-app
Den här referensen innehåller information om den [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md), som är färdiga samlingar med avsikter och entiteter som LUIS erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), däremot starta utan avsikter och modeller. Du kan lägga till några fördefinierade domän avsikter och entiteter till en anpassad modell.

## <a name="supported-domains-across-cultures"></a>Domäner som stöds över kulturer

Den enda kulturen som stöds är engelska. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Entitetstyp|description|
|--|--|
|Kalender|Kalender är allt om personliga möten och avtalade tider, _inte_ offentliga händelser (t. ex. världs omfattande scheman, händelse kalendrar i Seattle) eller allmänna kalendrar (till exempel vilken dag som är den idag, vad som händer när den är arbets dag).|
|Kommunikation|Förfrågningar om att ringa, skicka texter eller snabb meddelanden, söka efter och lägga till kontakter och andra kommunikations-relaterade begär Anden (vanligt vis utgående). Kontakt namn endast frågor tillhör inte kommunikations domän.|
|Email|E-post är en under domän till kommunikations domänen. Det innehåller främst förfrågningar om att skicka och ta emot meddelanden via e-post.|
|HomeAutomation|HomeAutomation-domänen innehåller avsikter och entiteter som är relaterade till att kontrol lera smarta hem enheter. Det stöder huvudsakligen kontroll kommandot som rör indikerings-och luft konditionering, men har vissa generaliseringar för andra elektriska apparater.|
|Anteckningar|Antecknings domän ger intentor och entiteter för att skapa anteckningar och skriva ned objekt för användare.|
|Platser|Platser innefattar företag, institutioner, restauranger, offentliga utrymmen och adresser. Domänen har stöd för att hitta och fråga om information om en offentlig plats, till exempel plats, drift timmar och avstånd.|
|RestaurantReservation|Restaurang reservations domänen stöder avsikter för att hantera reservationer för restauranger.|
|Att göra|Att göra-domän tillhandahåller typer av uppgifts listor för användare att lägga till, markera och ta bort sina att göra-objekt.|
|Samhällsservice|-Domänen är en allmän domän bland alla LUIS-fördefinierade modeller som innehåller gemensamma avsikter och yttranden i skillnads scenarier.|
|Väder|Väder domänen fokuserar på att kontrol lera väder förhållanden och rekommendationer med plats och tid eller kontroll tid efter väder förhållanden.|
|Webb|Webb domänen ger avsikt och entiteter för att söka efter en webbplats.|
