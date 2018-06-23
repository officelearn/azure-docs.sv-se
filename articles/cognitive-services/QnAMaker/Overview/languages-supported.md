---
title: Språk - frågor och svar om Maker - Azure kognitiva tjänster som stöds | Microsoft Docs
description: Lär dig mer om vilka språk som stöds för frågor och svar om Maker.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354939"
---
# <a name="supported-languages"></a>Språk som stöds

Språk för en kunskapsbas påverkar frågor och svar om Maker möjlighet att automatiskt extrahera frågor och svar från [källor](../Concepts/data-sources-supported.md), samt för resultaten frågor och svar om Maker ger svar på användarfrågor.

## <a name="auto-extraction"></a>Automatisk extrahering
Frågor och svar om Maker stöder fråga/svar extrahering i sidan alla språk, men effektivitet uppackningen är mycket högre för följande språk som frågor och svar om Maker använder nyckelord för att identifiera frågor.

|Språk som stöds| Nationella inställningar|
|-----|----|
|Svenska|SV-*|
|Franska|fr-*|
|Italienska|IT-*|
|Tyska|Tyskland-*|
|Spanska|ES-*|

## <a name="query-matching-and-relevance"></a>Fråga matchar och relevans
Frågor och svar om Maker beror på [språkanalys](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support) i Azure search för att tillhandahålla resultat. Särskilda nytt rangordnings funktioner är tillgängliga för SV-* språk som möjliggör bättre betydelse.

Frågor och svar om Maker automatiskt identifierar språket i kunskapsbasen vid skapandet och anger analyzer därefter. Du kan skapa knowledge baser på följande språk. Läs [detta](../How-To/language-knowledge-base.md) för mer information om hur frågor och svar om Maker hanterar språk.


> [!Tip]
> Språkanalys, när, kan inte ändras. Dessutom språk analyzer gäller för alla knowledge grunderna i en [frågor och svar om Maker tjänsten](../How-To/set-up-qnamaker-service-azure.md). Om du planerar att ha kunskap databaser på olika språk, bör du skapa dem under separata frågor och svar om Maker tjänster.

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
