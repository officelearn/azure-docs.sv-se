---
title: Vad är Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder Content Moderator för att spåra, flagga, utvärdera och filtrera olämpligt material i användargenererat innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f28f2bcf5d04c9a6354b8135bd39546b9d8b9bf3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404302"
---
# <a name="what-is-azure-content-moderator"></a>Vad är Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator är en kognitiv tjänst som kontrollerar text-, bild- och videoinnehåll för material som kan vara stötande, riskabelt eller på annat sätt oönskat. När det här materialet hittas använder tjänsten lämpliga etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare. Se avsnittet [Moderering API:er](#moderation-apis) om du vill veta mer om vad de olika innehållsflaggorna anger.

## <a name="where-its-used"></a>Var den används

Följande är några scenarier där en programutvecklare eller ett team kan använda Content Moderator:

- Online marknadsplatser som modererar produktkataloger och annat användargenererat innehåll.
- Spelföretag som modererar användargenererade spelartefakter och chattrum.
- Sociala meddelandeplattformar som modererar bilder, text och videor som lagts till av användarna.
- Företag medieföretag som implementerar centraliserad moderering för sitt innehåll.
- K-12 utbildningslösningsleverantörer filtrerar bort innehåll som är olämpligt för elever och lärare.

> [!NOTE]
> Du kan inte använda Content Moderator för att identifiera olagliga bilder för utnyttjande av barn. Kvalificerade organisationer kan dock använda [Cloud Service PhotoDNA](https://www.microsoft.com/photodna "Microsoft PhotoDNA-molntjänst") för att söka efter den här typen av innehåll.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator-tjänsten består av flera webbtjänst-API:er som är tillgängliga via både REST-anrop och en .NET-SDK. Den innehåller också granskningsverktyget, som gör det möjligt för mänskliga granskare att hjälpa tjänsten och förbättra eller finjustera dess modereringsfunktion.

## <a name="moderation-apis"></a>API:er för moderering

Content Moderator-tjänsten innehåller Moderation API:er, som kontrollerar innehåll efter material som kan vara olämpligt eller stötande.

![blockdiagram för API:er för moderering av innehållsmodererator](images/content-moderator-mod-api.png)

I följande tabell beskrivs de olika typerna av api:er för moderering.

| API-grupp | Beskrivning |
| ------ | ----------- |
|[**Textmodering**](text-moderation-api.md)| Söker igenom text efter stötande innehåll, sexuellt explicit eller suggestivt innehåll, svordomar och personuppgifter.|
|[**Anpassade termlistor**](try-terms-list-api.md)| Söker igenom text mot en anpassad lista med termer tillsammans med de inbyggda termerna. Använd anpassade listor för att blockera eller tillåta innehåll baserat på dina egna innehållsprinciper.|  
|[**Bildmoderering**](image-moderation-api.md)| Söker igenom bilder efter vuxet eller olämpligt innehåll, identifierar text i bilder med funktionen för optisk teckenläsning (OCR) och identifierar ansikten.|
|[**Anpassade bildlistor**](try-image-list-api.md)| Söker igenom bilder mot en anpassad lista med bilder. Använd anpassade bildlistor för att filtrera bort instanser av ofta återkommande innehåll som du inte vill klassificera igen.|
|[**Moderering av video**](video-moderation-api.md)| Söker igenom videor efter vuxet eller olämpligt innehåll och returnerar tidsmarkörer för sådant innehåll.|

## <a name="review-apis"></a>Omdömes-API:er

Med gransknings-API:erna kan du integrera din modereringspipeline med mänskliga granskare. Använd åtgärderna [Jobb,](review-api.md#jobs) [Granskning](review-api.md#reviews)och [Arbetsflöde](review-api.md#workflows) för att skapa och automatisera arbetsflöden i loop med [granskningsverktyget](#review-tool) (nedan).

> [!NOTE]
> Arbetsflödes-API:et är ännu inte tillgängligt i .NET SDK men kan användas med REST-slutpunkten.

![blockera diagram för API:er för granskning av innehållsmoderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Granskningsverktyg

Tjänsten Content Moderator innehåller även det webbaserade [granskningsverktyget](Review-Tool-User-Guide/human-in-the-loop.md), som är värd för innehållsgranskningar för mänskliga moderatorer att bearbeta. Den mänskliga insatsen tränar inte tjänsten, men det kombinerade arbetet i tjänsten och mänskliga granskningsteam gör det möjligt för utvecklare att hitta rätt balans mellan effektivitet och noggrannhet. Granskningsverktyget ger också en användarvänlig klientdel för flera content moderator-resurser.

![Startsida för granskningsverktyg för innehållsmodererator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Content Moderator-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Komma igång med Content Moderator-tjänsten genom att följa instruktionerna för att [prova Content Moderator på webben](quick-start.md).
