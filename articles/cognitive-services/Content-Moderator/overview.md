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
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 82dc81c540115f08e57f87e63184e1e895c5e4fe
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834549"
---
# <a name="what-is-azure-content-moderator"></a>Vad är Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator är en kognitiv tjänst som kontrollerar text-, bild-och video innehåll för material som kan vara stötande, riskfyllda eller på annat sätt olämpligt. När det här materialet hittas tillämpar tjänsten lämpliga etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare. I avsnittet [moderator-API: er](#moderation-apis) kan du läsa mer om vad olika innehålls flaggor visar.

## <a name="where-its-used"></a>Där den används

Följande är några scenarier där en programutvecklare eller ett team kan använda Content Moderator:

- Marknads platser på marknaden som är till för stora produkt kataloger och annat innehåll som skapats av användare.
- Spel företag som är måttligt användardefinierade spel artefakter och chat-rum.
- Sociala meddelande plattformar som är måttliga bilder, text och videor som har lagts till av deras användare.
- Företags medie företag som implementerar Central moderator för innehållet.
- K-12 utbildnings lösnings leverantörer filtrerar bort innehåll som är olämpligt för studenter och lärare.

> [!NOTE]
> Du kan inte använda Content Moderator för att identifiera ogiltiga underordnade användnings avbildningar. Kvalificerade organisationer kan dock använda [moln tjänsten PhotoDNA](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud service") för den här typen av innehåll.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator-tjänsten består av flera webbtjänst-API:er som är tillgängliga via både REST-anrop och en .NET-SDK. Den innehåller också gransknings verktyget, som gör det möjligt för mänskliga granskare att hjälpa tjänsten och förbättra eller finjustera sin redigerings funktion.

## <a name="moderation-apis"></a>API:er för moderering

I tjänsten Content Moderator ingår redigerings-API: er som kontrollerar innehåll för material som kan vara olämpligt eller olämpligt.

![block diagram för Content Moderator moderator-API: er](images/content-moderator-mod-api.png)

I följande tabell beskrivs de olika typerna av redigerings-API: er.

| API-grupp | Beskrivning |
| ------ | ----------- |
|[**Textmoderering**](text-moderation-api.md)| Söker efter stötande innehåll, sexuellt stötande eller förslag på innehåll, svordomar och personliga data.|
|[**Anpassade termlistor**](try-terms-list-api.md)| Söker igenom text mot en anpassad lista med villkor tillsammans med de inbyggda villkoren. Använd anpassade listor för att blockera eller tillåta innehåll baserat på dina egna innehållsprinciper.|  
|[**Bildmoderering**](image-moderation-api.md)| Söker igenom bilder efter vuxet eller olämpligt innehåll, identifierar text i bilder med funktionen för optisk teckenläsning (OCR) och identifierar ansikten.|
|[**Anpassade bildlistor**](try-image-list-api.md)| Söker igenom bilder mot en anpassad lista med bilder. Använd anpassade bildlistor för att filtrera bort instanser av ofta återkommande innehåll som du inte vill klassificera igen.|
|[**Videomoderering**](video-moderation-api.md)| Söker igenom videor efter vuxet eller olämpligt innehåll och returnerar tidsmarkörer för sådant innehåll.|

## <a name="review-apis"></a>Omdömes-API:er

Med gransknings-API: erna kan du integrera din moderator med mänsklig granskare. Använd [jobb](review-api.md#jobs)-, [gransknings](review-api.md#reviews)-och [arbets flödes](review-api.md#workflows) åtgärderna för att skapa och automatisera arbets flöden med mänsklig verksamhet med [gransknings verktyget](#review-tool) (nedan).

> [!NOTE]
> Arbets flödes-API: t är inte tillgängligt ännu i .NET SDK men kan användas med REST-slutpunkten.

![block diagram för Content Moderator gransknings-API: er](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Granskningsverktyg

Tjänsten Content Moderator innehåller också det webbaserade [gransknings verktyget](Review-Tool-User-Guide/human-in-the-loop.md)som är värd för innehålls granskningar för de mänskliga moderatorerna att bearbeta. Mänsklig inblandning tränar inte tjänsten, men det kombinerade arbetet i tjänsten och gruppen för mänsklig granskning gör det möjligt för utvecklare att skapa rätt balans mellan effektivitet och exakthet. Gransknings verktyget tillhandahåller också en användarvänlig klient för flera Content Moderator-resurser.

![Start sida för Content Moderator gransknings verktyget](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Content Moderator-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Komma igång med Content Moderator-tjänsten genom att följa instruktionerna för att [prova Content Moderator på webben](quick-start.md).
