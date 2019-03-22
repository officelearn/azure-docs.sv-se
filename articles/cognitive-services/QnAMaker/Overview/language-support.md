---
title: Språkstöd – QnA Maker
titleSuffix: Azure Cognitive Services
description: Språk för en kunskapsbas påverkar QnA Maker möjligheten att automatiskt extrahera frågor och svar från källor samt relevans resultatets QnA Maker ger svar på användarfrågor. En lista över kulturen, naturligt språk som stöds av QnA Maker för kunskapsbasen. Blanda inte språk i samma knowledge base.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 820a9ae0df91fba8cf00764428867bec6196841a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335869"
---
# <a name="language-support-for-qna-maker"></a>Språkstöd för QnA Maker

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

## <a name="primary-language-detection"></a>Primär språkidentifiering

Det primära språket som används för identifiering av har angetts för QnA Maker-resursen och alla kunskapsbaser som skapats på den aktuella, när det första dokumentet eller URL: en läggs till i första knowledge base. Språket kan inte ändras. 

Om du planerar att ha stöd för flera språk, som de behöver ha en ny QnA Maker-resurs för varje språk. Lär dig hur du [skapa en kunskapsbas för språk-baserade QnA Maker](/how-to/language-knowledge-base.md).  

Kontrollera det primära språket med följande steg:

1. Logga in på [Azure Portal](http://portal.azure.com).  
1. Leta efter och välj den Azure Search-resurs som skapats som en del av din QnA Maker-resurs. Azure Search-resursnamn börjar med samma namn som QnA Maker-resursen och kommer att ha typen **söktjänsten**. 
1. Från den **översikt** sidan Sök resursens väljer **index**. 
1. Välj den **testkb** index.
1. Välj den **fält** fliken. 
1. Visa den **Analyzer** kolumnen för den **frågor** och **svar** fält. 


## <a name="query-matching-and-relevance"></a>Fråga matchar och relevans
QnA Maker beror på [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) i Azure search för att tillhandahålla resultat. Särskilda igen rangordna funktioner är tillgängliga för En-* språk som gör bättre relevant.

Funktioner för Azure Search finns på par för språk som stöds, har QnA Maker en ytterligare rankningen som är placerad ovanför Azure-sökresultaten. I den här rankningen modellen använder vi vissa särskilda semantiska och word-baserade funktioner i en-*, som ännu inte är tillgänglig för andra språk. Vi gör inte dessa funktioner tillgängliga, eftersom de är en del av en intern fungerande QnA Maker rankningen. 

QnA Maker [upptäcker automatiskt språket i kunskapsbasen](#primary-language-detection) när du skapar och ställer in analysatorn därefter. Du kan skapa kunskapsbaser på följande språk. 

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
