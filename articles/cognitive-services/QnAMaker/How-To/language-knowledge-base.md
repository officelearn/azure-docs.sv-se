---
title: Språk koncept – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker stöder innehåll i kunskaps basen på många språk. Men varje QnA Maker tjänst bör vara reserverad för ett enda språk. Den första kunskaps bas som skapats, som riktar sig mot en viss QnA Maker tjänst, anger språket för den tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220637"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språk stöd för kunskaps bas innehåll för QnA Maker

Språk för tjänsten väljs när du skapar den första kunskaps basen i resursen. Alla ytterligare kunskaps baser i resursen måste vara på samma språk.

Språket bestämmer relevansen hos resultaten QnA Maker ger svar på användar frågor.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Ett språk för alla kunskaps baser i resurs

Med QnA Maker kan du välja språk för din QnA-tjänst när du skapar den första kunskaps basen. Alla kunskaps baser i en QnA Maker-resurs måste vara på samma språk. Det går inte att ändra det här språket.

Att skapa kunskaps baser på olika språk i en resurs påverkar negativt relevansen för resultaten QnA Maker ger svar på användar frågor.

Granska en lista över [språk som stöds](../overview/language-support.md#languages-supported) och hur språk påverkar [matchning och relevans](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Välj språk när du skapar den första kunskaps basen

Val av språk är en del av stegen för att skapa den första kunskaps basen i en resurs.

![QnA Maker Portal skärm bild av val av språk för den första kunskaps basen](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Fråga matchar och relevans
QnA Maker är beroende av [Azure kognitiv sökning språk analys](https://docs.microsoft.com/rest/api/searchservice/language-support) verktyg för att ge resultat.

Även om Azure Kognitiv sökning-funktionerna är på parivärde för språk som stöds, har QnA Maker ytterligare rangordning som ligger ovanför Azure Search-resultaten. I den här ranknings modellen använder vi vissa särskilda semantiska och Word-baserade funktioner på följande språk.

|Språk med ytterligare rangordning|
|--|
|Kinesiska|
|Tjeckiska|
|Nederländska|
|Svenska|
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

## <a name="verify-language"></a>Verifiera språk

Du kan kontrol lera språket för din QnA Maker resurs på sidan tjänst inställningar i QnA Maker.

![QnA Maker Portal skärm bild av sidan tjänst inställningar](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera en kunskaps bas](../Tutorials/migrate-knowledge-base.md)
