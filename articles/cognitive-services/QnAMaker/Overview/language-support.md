---
title: Språk stöd – QnA Maker
titleSuffix: Azure Cognitive Services
description: En lista över kultur, naturliga språk som stöds av QnA Maker för din kunskaps bas. Blanda inte språk i samma kunskaps bas.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650907"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Språk stöd för en QnA Maker resurs-och kunskaps baser

Språk för tjänsten väljs när du skapar den första kunskaps basen i resursen. Alla ytterligare kunskaps baser i resursen måste vara på samma språk. 

Språket bestämmer relevansen hos resultaten QnA Maker ger svar på användar frågor. QnA Maker resursen och alla kunskaps baser i resursen, stöder ett enda språk. Det enda språket är nödvändigt för att ge bästa svars resultat för en fråga.

## <a name="single-language-per-resource"></a>Enskilt språk per resurs

Tänk också på följande:

* En QnA Maker tjänst, och alla dess kunskaps baser, stöder bara ett språk.
* Språket anges explicit när den första kunskaps basen i tjänsten skapas
* Språket bestäms av de filer och URL: er som läggs till när kunskaps basen skapas
* Det går inte att ändra språket för andra kunskaps banker i tjänsten
* Språket används av Kognitiv sökning tjänsten (Ranging #1) och QnA Maker tjänsten (Ranging #2) för att generera det bästa svaret på en fråga

## <a name="supporting-multiple-languages"></a>Stöd för flera språk

Om du behöver stöd för ett kunskaps bas system, som innehåller flera språk, kan du:

* Använd [tjänsten Translator](../../translator/translator-info-overview.md) för att översätta en fråga till ett språk innan du skickar frågan till din kunskaps bas. På så sätt kan du fokusera på kvaliteten på ett enda språk och kvaliteten på de alternativa frågorna och svaren.
* Skapa en QnA Maker resurs och en kunskaps bas i resursen för varje språk. På så sätt kan du hantera separata alternativa frågor och svars text som är mer nyanserade för varje språk. Detta ger dig mycket mer flexibilitet men kräver en mycket högre underhålls kostnad när frågorna eller Svaren ändras på alla språk.

Granska [språk som stöds](../overview/language-support.md) för QNA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Stöd för varje språk med en QnA Maker-resurs

* Skapa en QnA Maker resurs för varje språk
* Lägg endast till filer och URL: er för språket
* Använd en namngivnings konvention för resursen för att identifiera språket. Ett exempel är `qna-maker-fr` för alla kunskaps baser om franska dokument


## <a name="languages-supported"></a>Språk som stöds

Följande lista innehåller de språk som stöds för en QnA Maker-resurs. 

|Språk|
|--|
|Arabiska|
|Armeniska|
|Bangla|
|Baskiska|
|Bulgariska|
|Katalanska|
|Chinese_Simplified|
|Chinese_Traditional|
|Kroatiska|
|Tjeckiska|
|Danska|
|Nederländska|
|Engelska|
|Estniska|
|Finska|
|Franska|
|Galiciska|
|Tyska|
|Grekiska|
|Gujarati|
|Hebreiska|
|Hindi|
|Ungerska|
|Isländska|
|Indonesiska|
|Iriska|
|Italienska|
|Japanska|
|Kannada|
|Koreanska|
|Lettiska|
|Litauiska|
|Malayalam|
|Malajiska|
|Norska|
|Polska|
|Portugisiska|
|Punjabi|
|Rumänska|
|Ryska|
|Serbian_Cyrillic|
|Serbian_Latin|
|Slovakiska|
|Slovenska|
|Spanska|
|Svenska|
|Tamilska|
|Telugu|
|Thailändska|
|Turkiska|
|Ukrainska|
|Urdu|
|Vietnamesiska|

## <a name="query-matching-and-relevance"></a>Fråga matchning och relevans
QnA Maker är beroende av [Azure kognitiv sökning språk analys](https://docs.microsoft.com/rest/api/searchservice/language-support) verktyg för att ge resultat.

Även om Azure Kognitiv sökning-funktionerna är på parivärde för språk som stöds, har QnA Maker ytterligare rangordning som ligger ovanför Azure Search-resultaten. I den här ranknings modellen använder vi vissa särskilda semantiska och Word-baserade funktioner på följande språk.

|Språk med ytterligare rangordning|
|--|
|Kinesiska|
|Tjeckiska|
|Nederländska|
|Engelska|
|Franska|
|Tyska|
|Ungerska|
|Italienska|
|Japanska|
|Koreanska|
|Polska|
|Portugisiska|
|Spanska|
|Svenska|

Den här ytterligare rangordningen är en intern bearbetning av QnA Makers rang.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Val av språk](../how-to/language-knowledge-base.md)
