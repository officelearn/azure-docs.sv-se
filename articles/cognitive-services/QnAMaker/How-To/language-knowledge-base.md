---
title: Språkbegrepp - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker stöder kunskapsbasinnehåll på många språk. Varje QnA Maker-tjänst bör dock reserveras för ett enda språk. Den första kunskapsbasen som skapats och som riktar sig till en viss QnA Maker-tjänst anger språket för den tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220637"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språkstöd för kunskapsbasinnehåll för QnA Maker

Språk för tjänsten väljs när du skapar den första kunskapsbasen i resursen. Alla ytterligare kunskapsbaser i resursen måste vara på samma språk.

Språket avgör relevansen av de resultat som QnA Maker tillhandahåller som svar på användarfrågor.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Ett språk för alla kunskapsbaser i resurs

Med QnA Maker kan du välja språk för din QnA-tjänst och samtidigt skapa den första kunskapsbasen. För alla kunskapsbaser i en QnA Maker-resurs måste alla vara på samma språk. Det här språket kan inte ändras.

Att skapa kunskapsbaser på olika språk på en resurs påverkar negativt relevansen av de resultat som QnA Maker tillhandahåller som svar på användarfrågor.

Granska en lista över [språk som stöds](../overview/language-support.md#languages-supported) och hur språk påverkar [matchning och relevans](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Välj språk när du skapar den första kunskapsbasen

Språkval är en del av stegen för att skapa den första kunskapsbasen i en resurs.

![QnA Maker portal skärmdump av att välja språk för första kunskapsbas](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Frågematchning och relevans
QnA Maker är beroende av [Azure Cognitive Search språkanalysatorer](https://docs.microsoft.com/rest/api/searchservice/language-support) för att ge resultat.

Medan Azure Cognitive Search-funktionerna är i paritet för språk som stöds, har QnA Maker ytterligare en ranker som sitter ovanför Azure-sökresultaten. I denna rankermodell använder vi några speciella semantiska och ordbaserade funktioner på följande språk.

|Språk med ytterligare ranker|
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
|Koreansk|
|Polska|
|Portugisiska|
|Spanska|
|Svenska|

Denna extra ranking är ett internt arbete av QnA Maker's ranker.

## <a name="verify-language"></a>Verifiera språk

Du kan verifiera språket för din QnA Maker-resurs från sidan för tjänstinställningar i QnA Maker.

![QnA Maker-portal skärmbild av sidan Tjänstinställningar](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
