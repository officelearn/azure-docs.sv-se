---
title: Artificiell intelligens
description: LUIS använder artificiell intelligens för att tillhandahålla språk förståelse för dina data baserat på det schema som du har definierat.
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802662"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Artificiell intelligens i Language Understanding (LUIS)

LUIS använder artificiell intelligens för att tillhandahålla naturliga språk förståelse (NLU) för dina data baserat på det schema som du har definierat.

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

Naturlig Language Understanding (NLU) är ett speciellt underavsnitt av NLP (naturlig Language Processing).

Bearbetning av naturligt språk är ett bredare koncept som hanterar alla former av bearbetning av text data, detta omfattar bland annat:

* Tokenisering
* Del av tal märkning (POS)
* Segmentering
* Morfologisk analys
* Semantisk likhet
* Avkurs
* Översättning

## <a name="natural-language-processing-in-luis"></a>Bearbetning av naturligt språk i LUIS

Bearbetning av naturligt språk är tillgängligt för LUIS-appen på följande sätt:
* [Natur språks förståelse](#natural-language-understanding) (Luis)
* Konfigurerbara NLP-aspekter i LUIS:
    * [Tokenisering](luis-language-support.md#tokenization)
    * [API-inställningar](luis-reference-application-settings.md) för den här inställningen för dia kritiska tecken, interpunktion och ord former
* Efter bearbetning av frågan uttryck som tillhandahålls av andra [Cognitive Services](../Welcome.md) , till exempel:
    * [Översättning](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>Kunskap om naturligt språk

NLU är möjligheten att _omvandla_ en språklig instruktion till en åter givning som gör det möjligt för dig att förstå dina användare naturligt. Naturligt språk att förstå är ett mycket utmanande problem och definieras som ett _AI-hårt_ problem.

LUIS är avsett att fokusera på avsikt och extraktion, men detta inkluderar att kunna identifiera:
* Vad användaren vill
* Vad de pratar om.

LUIS har lite eller ingen kunskap om de bredare _NLP_ -aspekterna, till exempel semantisk likhet, utan explicit identifiering i exempel. Till exempel är följande tokens (ord) tre olika saker tills de används i liknande kontexter i de exempel som tillhandahålls:

* handlar
* köpa
* åter

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [utvecklings livs cykeln](luis-concept-app-iteration.md) för en Luis-app