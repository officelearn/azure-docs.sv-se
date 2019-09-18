---
title: Språkstöd – QnA Maker
titleSuffix: Azure Cognitive Services
description: Språket i en kunskaps bas påverkar QnA Maker möjlighet att automatiskt extrahera frågor och svar från källor, samt relevansen för resultatet QnA Maker ger svar på användar frågor. En lista över kulturen, naturligt språk som stöds av QnA Maker för kunskapsbasen. Blanda inte språk i samma knowledge base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066667"
---
# <a name="language-support-for-qna-maker"></a>Språk stöd för QnA Maker

Språk för en kunskapsbas påverkar QnA Maker möjligheten att automatiskt extrahera frågor och svar från [källor](../Concepts/data-sources-supported.md), samt relevans resultatets QnA Maker ger svar på användarfrågor.

## <a name="auto-extraction"></a>Automatisk extrahering
QnA Maker stöder extrahering av fråga/svar i alla på sidan, men effektiviteten i extraheringen är mycket högre för följande språk som QnA Maker använder nyckelord för att identifiera frågor.

|Språk som stöds| Nationell inställning|
|-----|----|
|Svenska|SV-*|
|Franska|fr-*|
|Italienska|IT-*|
|Tyska|Tyskland – *|
|Spanska|ES – *|

## <a name="primary-language-detection"></a>Primär språk identifiering

Det primära språket som används för identifiering anges för QnA Maker resursen och alla kunskaps banker som skapats på den resursen, när det första dokumentet eller URL: en läggs till i den första kunskaps basen. Det går inte att ändra språket. 

Om användaren planerar att stödja flera språk måste de ha en ny QnA Maker-resurs för varje språk. Lär dig hur du [skapar en språkbaserad QNA Maker kunskaps bas](../how-to/language-knowledge-base.md).  

Verifiera det primära språket med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com).  
1. Leta efter och välj den Azure Search resurs som skapats som en del av din QnA Maker-resurs. Azure Search resurs namnet kommer att börja med samma namn som den QnA Maker resursen och har typ **Sök tjänsten**. Kom ihåg att endast en QnA Maker resurs kan länkas till en Azure Search resurs.
1. På sidan **Översikt** i Sök resursen väljer du **index**. 
1. Välj **testkb** -indexet.
1. Välj fliken **fält** . 
1. Visa kolumnen **Analyzer** för **frågorna** och **svars** fälten. 


## <a name="query-matching-and-relevance"></a>Fråga matchar och relevans
QnA Maker beror på [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) i Azure search för att tillhandahålla resultat. Särskilda igen rangordna funktioner är tillgängliga för En-* språk som gör bättre relevant.

Funktioner för Azure Search finns på par för språk som stöds, har QnA Maker en ytterligare rankningen som är placerad ovanför Azure-sökresultaten. I den här ranknings modellen använder vi vissa särskilda semantiska och Word-baserade funktioner i en-*, som ännu inte är tillgängliga för andra språk. De här funktionerna är inte tillgängliga eftersom de är en del av den interna bearbetningen av QnA Makers rang. 

QnA Maker [automatiskt identifierar språket i kunskaps basen](#primary-language-detection) när det skapas och ställer in analysen enligt detta. Du kan skapa kunskapsbaser på följande språk. 

|Språk som stöds|
|-----|
|Arabiska|
|Armeniska|
Bengali|
|Baskiska|
|Bulgariska|
|Katalanska|
|Chinese_Simplified|
|Chinese_Traditional|
|Kroatiska|
|Tjeckiska|
|Danska|
|Nederländska|
|Svenska|
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
|Thai|
|Turkiska|
|Ukrainska|
|Urdu|
|Vietnamesiska|
