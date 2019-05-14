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
ms.openlocfilehash: 7e9c12c7da701fb627c51373e57f870d3fc77ac5
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551330"
---
# <a name="what-is-azure-content-moderator"></a>Vad är Azure Content Moderator?

Azure Content Moderator API är en kognitiv tjänst som kontrollerar innehåll med text, bilder och video efter material som är potentiellt stötande, riskfyllt eller på annat sätt oönskat. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare. Se den [API: er för Moderering](#moderation-apis) tyda på att ta reda på mer om olika innehåll flaggar.

## <a name="where-it-is-used"></a>Var verktyget kan användas

Följande är några scenarier där en programutvecklare eller ett team kan använda Content Moderator:

- Marknadsplatser online som modererar produktkataloger och annat användargenererat innehåll
- Spelföretag som modererar användargenererade spelartefakter och chattrum
- Plattformar för sociala meddelanden som modererar bilder, text och videor som läggs till av användarna
- Medieföretag som implementerar centraliserad moderering för sitt innehåll
- K-12-utbildningslösningsföretag som filtrerar bort innehåll som är olämpligt för elever och lärare

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator-tjänsten består av flera webbtjänst-API:er som är tillgängliga via både REST-anrop och en .NET-SDK. Den innehåller även verktyg för mänsklig granskning, vilket gör att mänskliga granskare kan bistå tjänsten och förbättra eller finjustera dess modereringsfunktion.

## <a name="moderation-apis"></a>API:er för moderering

Tjänsten Content Moderator innehåller Moderering API: er, som kontrollerar material som är potentiellt olämpligt eller stötande innehåll.

![blockdiagram för Content Moderator moderering API: er](images/content-moderator-mod-api.png)

I följande tabell beskrivs de olika typerna av moderering API: er.

| API-gruppen | Beskrivning |
| ------ | ----------- |
|[**Textmoderering**](text-moderation-api.md)| Söker igenom text efter stötande innehåll, sexuellt explicit eller något innehåll, svordomar och personliga data.|
|[**Anpassade termlistor**](try-terms-list-api.md)| Söker igenom text mot en anpassad lista med termer utöver de inbyggda termerna. Använd anpassade listor för att blockera eller tillåta innehåll baserat på dina egna innehållsprinciper.|  
|[**Bildmoderering**](image-moderation-api.md)| Söker igenom bilder efter vuxet eller olämpligt innehåll, identifierar text i bilder med funktionen för optisk teckenläsning (OCR) och identifierar ansikten.|
|[**Anpassade bildlistor**](try-image-list-api.md)| Söker igenom bilder mot en anpassad lista med bilder. Använd anpassade bildlistor för att filtrera bort instanser av ofta återkommande innehåll som du inte vill klassificera igen.|
|[**Videomoderering**](video-moderation-api.md)| Söker igenom videor efter vuxet eller olämpligt innehåll och returnerar tidsmarkörer för sådant innehåll.|

## <a name="review-apis"></a>Omdömes-API:er

Granska API: erna kan du integrera din pipeline för moderering med mänsklig granskare. Använd den [jobb](review-api.md#jobs), [granskningar](review-api.md#reviews), och [arbetsflöde](review-api.md#workflows) åtgärder för att skapa och automatisera arbetsflöden human-i-the-loop med den [granskningsverktyget](#the-review-tool) () nedan).

> [!NOTE]
> API: et för arbetsflödet ännu inte är tillgängliga i .NET SDK, men kan användas med REST-slutpunkten.

![blockdiagram för Content Moderator granskar API: er](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Granskningsverktyget

Content Moderator tjänsten omfattar även den webbaserade [granskningsverktyget](Review-Tool-User-Guide/human-in-the-loop.md), som är värd för innehållet går igenom för mänskliga moderatorer att bearbeta. Mänskliga indata tränar inte tjänsten, men det kombinerade arbetet från tjänsten och mänskliga granskningsteam gör att utvecklare kan åstadkomma rätt balans mellan effektivitet och noggrannhet. Granskningsverktyget innehåller också ett användarvänligt klientdel för en mängd olika resurser för Content Moderator.

![Hemsida för Content Moderator-verktyget för mänsklig granskning](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Content Moderator-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Komma igång med Content Moderator-tjänsten genom att följa instruktionerna för att [prova Content Moderator på webben](quick-start.md).