---
title: Fördefinierad domänreferens
titleSuffix: Azure
description: Referens för de fördefinierade domäner som är färdiga samlingar av avsikter och entiteter från Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233888"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referens för fördefinierade för LUIS-appen
Den här referensen innehåller information om den [fördefinierade domäner](luis-how-to-use-prebuilt-domains.md), som är färdiga samlingar med avsikter och entiteter som LUIS erbjuder.

[Anpassade domäner](luis-how-to-start-new-app.md), däremot starta utan avsikter och modeller. Du kan lägga till några fördefinierade domän avsikter och entiteter till en anpassad modell.

# <a name="supported-domains-across-cultures"></a>Domäner över kulturer som stöds

Den enda stödda kulturen är engelska. 

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
|Kalender|Kalender är något om personliga möten och möten, _inte_ publika händelser (till exempel world cup scheman, Seattle händelsekalendrar eller allmänna kalendrar (till exempel vilken dag är det idag vad fall börjar, när är Labor Day).|
|Kommunikation|Begäranden om att göra anrop, skicka skickar ett SMS eller snabbmeddelanden, hitta och Lägg till kontakter och olika andra kommunikation skickas (utgående Allmänt). Kontaktperson endast frågor hör inte till domänen för kommunikation.|
|E-post|Email is a subdomain of the Communication domain. Den innehåller främst begäranden för att skicka och ta emot meddelanden via e-postmeddelanden.|
|HomeAutomation|HomeAutomation domänen innehåller avsikter och entiteter relaterade till att styra smart home enheter. Den stöder huvudsakligen kommandot kontroll som rör lamporna och luftkonditionering, men den har vissa generalisering funktioner för andra elektriska apparater.|
|Anteckningar|Obs domänen ger avsikter och entiteter för att skapa anteckningar och skriva ned objekt för användare.|
|Platser|Platser är företag, institutioner, restauranger, offentliga blanksteg och adresser. Domänen har stöd för plats att söka efter och frågar om information om en offentlig plats, till exempel plats, operativsystem timmar och avstånd.|
|RestaurantReservation|Restaurang reservation domän stöder avsikter för hantering av reservationer för restauranger.|
|ToDo|Domän för att göra innehåller typer av uppgiftslistor för användare att lägga till, markera och ta bort sina att göra-objekt.|
|Samhällsservice|Hjälpmedel domän är en allmän domän bland alla THOMAS fördefinierade modeller som innehåller vanliga avsikter och yttranden i skillnaden scenarier.|
|Väder|Väder domän fokuserar på kontrollerar väderförändring och rekommendationerna med plats och tid eller kontrollerar tid av väderförhållanden.|
|Webb|Web-domänen innehåller avsikt och entiteter för att söka efter en webbplats.|
