---
title: Språkstöd – QnA Maker
titleSuffix: Azure Cognitive Services
description: En lista med naturligt språk som stöds av QnA Maker.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/09/2018
ms.author: tulasim
ms.openlocfilehash: 8c47c4a59f03328b1dc8d3df7771bac81864bb34
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566643"
---
# <a name="language-and-region-support-for-qna-maker"></a>Stöd för språk och din region för QnA Maker

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

## <a name="query-matching-and-relevance"></a>Fråga matchar och relevans
QnA Maker beror på [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) i Azure search för att tillhandahålla resultat. Särskilda igen rangordna funktioner är tillgängliga för En-* språk som gör bättre relevant.

Funktioner för Azure Search finns på par för språk som stöds, har QnA Maker en ytterligare rankningen som är placerad ovanför Azure-sökresultaten. I den här rankningen modellen vi använder vissa semantiska special och word-baserade funktioner i en-*, som ännu inte är tillgänglig för andra språk. Vi gör inte dessa tillgänglig, eftersom de är en del av en intern fungerande rankningen. 

QnA Maker upptäcker automatiskt språket i kunskapsbasen när du skapar och ställer in analysatorn därefter. Du kan skapa kunskapsbaser på följande språk. Läs [detta](../How-To/language-knowledge-base.md) för mer information om hur QnA Maker hanterar språk.


> [!Tip]
> Språkanalysverktyg, ställa in en gång, kan inte ändras. Dessutom språkanalysverktyg gäller för alla kunskapsbaser i en [QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md). Om du planerar att ha kunskapsbaser på olika språk, bör du skapa dem under separata QnA Maker-tjänster.

|Språk som stöds|
|-----|
|Arabiska|
|Armeniska|, 
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
