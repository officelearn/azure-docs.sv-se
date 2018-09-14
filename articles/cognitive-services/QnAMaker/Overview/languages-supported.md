---
title: Språk som stöds – QnA Maker
titlesuffix: Azure Cognitive Services
description: Språk för en kunskapsbas påverkar QnA Maker möjligheten att automatiskt extrahera frågor och svar från källor samt relevans resultatets QnA Maker ger svar på användarfrågor.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: ee04733064ec4e3d131b800fe1f18b27e5127fe8
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572948"
---
# <a name="supported-languages"></a>Språk som stöds

Språk för en kunskapsbas påverkar QnA Maker möjligheten att automatiskt extrahera frågor och svar från [källor](../Concepts/data-sources-supported.md), samt relevans resultatets QnA Maker ger svar på användarfrågor.

## <a name="auto-extraction"></a>Automatisk extrahering
QnA Maker stöder extrahering av fråga/svar i alla på sidan, men effektiviteten i extraheringen är mycket högre för följande språk som QnA Maker använder nyckelord för att identifiera frågor.

|Språk som stöds| Nationella inställningar|
|-----|----|
|Svenska|SV-*|
|Franska|fr-*|
|Italienska|IT-*|
|Tyska|Tyskland – *|
|Spanska|ES – *|

## <a name="query-matching-and-relevance"></a>Fråga matchar och relevans
QnA Maker beror på [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) i Azure search för att tillhandahålla resultat. Särskilda igen rangordna funktioner är tillgängliga för En-* språk som gör bättre relevant.

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
