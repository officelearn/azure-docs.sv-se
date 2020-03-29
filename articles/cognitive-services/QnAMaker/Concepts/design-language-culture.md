---
title: Design för språk - QnA Maker
description: QnA Maker-resursen och alla kunskapsbaser i resursen stöder ett enda språk. Det enda språket är nödvändigt för att ge bästa möjliga svarsresultat för en fråga.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843428"
---
# <a name="design-knowledge-base-for-content-language"></a>Designkunskapsbas för innehållsspråk

QnA Maker-resursen och alla kunskapsbaser i resursen stöder ett enda språk. Det enda språket är nödvändigt för att ge bästa möjliga svarsresultat för en fråga.

## <a name="single-language-per-resource"></a>Ett språk per resurs

QnA Maker överväganden för språkstöd:

* En QnA Maker-tjänst, och alla dess kunskapsbaser, stöder endast ett språk.
* Språket anges uttryckligen när tjänstens första kunskapsbas skapas
* Språket bestäms utifrån de filer och webbadresser som läggs till när kunskapsbasen skapas
* Språket kan inte ändras för andra kunskapsbaser i tjänsten
* Språket används av tjänsten Cognitive Search (ranker #1) och QnA Maker-tjänsten (ranker #2) för att generera det bästa svaret på en fråga

## <a name="supporting-multiple-languages"></a>Stöd för flera språk

Om du behöver stödja ett kunskapsbassystem, som innehåller flera språk, kan du välja något av följande:

* Använd [tjänsten Översättningstext](../../translator/translator-info-overview.md) för att översätta en fråga till ett enda språk innan du skickar frågan till din kunskapsbas. Detta gör att du kan fokusera på kvaliteten på ett enda språk och kvaliteten på de alternativa frågorna och svaren.
* Skapa en QnA Maker-resurs och en kunskapsbas i den resursen för varje språk. På så sätt kan du hantera separata alternativa frågor och svara på text som är mer nyanserad för varje språk. Detta ger dig mycket mer flexibilitet men kräver en mycket högre underhållskostnad när frågorna eller svaren ändras på alla språk.

Granska [språk som stöds](../overview/language-support.md) för QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Stöd varje språk med en QnA Maker-resurs

* Skapa en QnA Maker-resurs för alla språk
* Lägg bara till filer och webbadresser för det språket
* Använd en namngivningskonvention för resursen för att identifiera språket. Ett exempel `qna-maker-fr` är för alla kunskapsbaser för franska dokument

## <a name="next-steps"></a>Nästa steg

Lär dig [begrepp](query-knowledge-base.md) om hur du frågar kunskapsbasen efter ett svar.