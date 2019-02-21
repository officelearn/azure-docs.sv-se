---
title: Vad är Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder Content Moderator för att spåra, flagga, utvärdera och filtrera olämpligt material i användargenererat innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 7a6424921b869428d1dbeffadd68e173a32d8821
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455254"
---
# <a name="what-is-azure-content-moderator"></a>Vad är Azure Content Moderator?

Azure Content Moderator API är en kognitiv tjänst som kontrollerar innehåll med text, bilder och video efter material som är potentiellt stötande, riskfyllt eller på annat sätt oönskat. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare. I avsnittet [Content Moderator-API:er](#content-moderator-apis) kan du lära dig mer om vad de olika innehållsflaggorna indikerar.

## <a name="where-it-is-used"></a>Var verktyget kan användas

Följande är några scenarier där en programutvecklare eller ett team kan använda Content Moderator:

- Marknadsplatser online som modererar produktkataloger och annat användargenererat innehåll
- Spelföretag som modererar användargenererade spelartefakter och chattrum
- Plattformar för sociala meddelanden som modererar bilder, text och videor som läggs till av användarna
- Medieföretag som implementerar centraliserad moderering för sitt innehåll
- K-12-utbildningslösningsföretag som filtrerar bort innehåll som är olämpligt för elever och lärare

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator-tjänsten består av flera webbtjänst-API:er som är tillgängliga via både REST-anrop och en .NET-SDK. Den innehåller även verktyg för mänsklig granskning, vilket gör att mänskliga granskare kan bistå tjänsten och förbättra eller finjustera dess modereringsfunktion.

![Blockdiagram för Content Moderator som visar API:er för moderering och granskning samt ett verktyg för mänsklig granskning](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>Content Moderator-API:er

Content Moderator-tjänsten innehåller API:er för följande scenarier.

| Åtgärd | Beskrivning |
| ------ | ----------- |
|[**Textmoderering**](text-moderation-api.md)| Söker igenom text efter stötande innehåll, sexuellt explicit eller suggestivt innehåll, olämpligt språk och personligt identifierbar information (PII).|
|[**Anpassade termlistor**](try-terms-list-api.md)| Söker igenom text mot en anpassad lista med termer utöver de inbyggda termerna. Använd anpassade listor för att blockera eller tillåta innehåll baserat på dina egna innehållsprinciper.|  
|[**Bildmoderering**](image-moderation-api.md)| Söker igenom bilder efter vuxet eller olämpligt innehåll, identifierar text i bilder med funktionen för optisk teckenläsning (OCR) och identifierar ansikten.|
|[**Anpassade bildlistor**](try-image-list-api.md)| Söker igenom bilder mot en anpassad lista med bilder. Använd anpassade bildlistor för att filtrera bort instanser av ofta återkommande innehåll som du inte vill klassificera igen.|
|[**Videomoderering**](video-moderation-api.md)| Söker igenom videor efter vuxet eller olämpligt innehåll och returnerar tidsmarkörer för sådant innehåll.|
|[**Granskning**](try-review-api-job.md)| Använd åtgärderna [Jobb](try-review-api-job.md), [Granskningar](try-review-api-review.md) och [Arbetsflöde](try-review-api-workflow.md) för att skapa och automatisera HITL-arbetsflöden (Human-In-The-Loop) i verktyget för mänsklig granskning. Arbetsflödes-API:et är inte ännu tillgängligt via .NET-SDK.|

### <a name="human-review-tool"></a>Verktyg för mänsklig granskning

Content Moderator-tjänsten omfattar även det webbaserade [verktyget för mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md). 

![Hemsida för Content Moderator-verktyget för mänsklig granskning](images/homepage.PNG)

Du kan använda gransknings-API:erna för att konfigurera teamgranskningar av innehåll med text, bilder och video enligt filter som du anger. Mänskliga moderatorer kan sedan göra fatta de slutgiltiga modereringsbesluten. Mänskliga indata tränar inte tjänsten, men det kombinerade arbetet från tjänsten och mänskliga granskningsteam gör att utvecklare kan åstadkomma rätt balans mellan effektivitet och noggrannhet.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet
Som med alla Cognitive Services bör utvecklare som använder Content Moderator-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Komma igång med Content Moderator-tjänsten genom att följa instruktionerna för att [prova Content Moderator på webben](quick-start.md).
