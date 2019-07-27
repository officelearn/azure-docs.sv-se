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
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: a78a92a33075a97ddadb2e1fe677b7ded541d12c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565581"
---
# <a name="what-is-azure-content-moderator"></a>Vad är Azure Content Moderator?

Azure Content Moderator API är en kognitiv tjänst som kontrollerar innehåll med text, bilder och video efter material som är potentiellt stötande, riskfyllt eller på annat sätt oönskat. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare. I avsnittet [moderator-API: er](#moderation-apis) kan du läsa mer om vad olika innehålls flaggor visar.

## <a name="where-it-is-used"></a>Var verktyget kan användas

Följande är några scenarier där en programutvecklare eller ett team kan använda Content Moderator:

- Marknads platser på marknaden som är till för stora produkt kataloger och annat innehåll som skapats av användare.
- Spel företag som är måttligt användardefinierade spel artefakter och chat-rum.
- Sociala meddelande plattformar som är måttliga bilder, text och videor som har lagts till av deras användare.
- Företags medie företag som implementerar Central moderator för innehållet.
- K-12 utbildnings lösnings leverantörer filtrerar bort innehåll som är olämpligt för studenter och lärare.

> [!NOTE]
> Du kan inte använda Content Moderator för att identifiera ogiltiga underordnade användnings avbildningar. Kvalificerade organisationer kan dock använda moln tjänsten [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA") för den här typen av innehåll.

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator-tjänsten består av flera webbtjänst-API:er som är tillgängliga via både REST-anrop och en .NET-SDK. Den innehåller även verktyg för mänsklig granskning, vilket gör att mänskliga granskare kan bistå tjänsten och förbättra eller finjustera dess modereringsfunktion.

## <a name="moderation-apis"></a>API:er för moderering

I tjänsten Content Moderator ingår redigerings-API: er som kontrollerar innehåll för material som kan vara olämpligt eller olämpligt.

![block diagram för Content Moderator moderator-API: er](images/content-moderator-mod-api.png)

I följande tabell beskrivs de olika typerna av redigerings-API: er.

| API-grupp | Beskrivning |
| ------ | ----------- |
|[**Textmoderering**](text-moderation-api.md)| Söker efter stötande innehåll, sexuellt stötande eller förslag på innehåll, svordomar och personliga data.|
|[**Anpassade termlistor**](try-terms-list-api.md)| Söker igenom text mot en anpassad lista med termer utöver de inbyggda termerna. Använd anpassade listor för att blockera eller tillåta innehåll baserat på dina egna innehållsprinciper.|  
|[**Bildmoderering**](image-moderation-api.md)| Söker igenom bilder efter vuxet eller olämpligt innehåll, identifierar text i bilder med funktionen för optisk teckenläsning (OCR) och identifierar ansikten.|
|[**Anpassade bildlistor**](try-image-list-api.md)| Söker igenom bilder mot en anpassad lista med bilder. Använd anpassade bildlistor för att filtrera bort instanser av ofta återkommande innehåll som du inte vill klassificera igen.|
|[**Videomoderering**](video-moderation-api.md)| Söker igenom videor efter vuxet eller olämpligt innehåll och returnerar tidsmarkörer för sådant innehåll.|

## <a name="review-apis"></a>Omdömes-API:er

Med gransknings-API: erna kan du integrera din moderator med mänsklig granskare. Använd [jobb](review-api.md#jobs)-, [gransknings](review-api.md#reviews)-och [arbets flödes](review-api.md#workflows) åtgärderna för att skapa och automatisera arbets flöden med mänsklig verksamhet med [gransknings verktyget](#the-review-tool) (nedan).

> [!NOTE]
> Arbets flödes-API: t är inte tillgängligt ännu i .NET SDK men kan användas med REST-slutpunkten.

![block diagram för Content Moderator gransknings-API: er](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Gransknings verktyget

Tjänsten Content Moderator innehåller också det webbaserade gransknings [verktyget](Review-Tool-User-Guide/human-in-the-loop.md)som är värd för innehålls granskningar för de mänskliga moderatorerna att bearbeta. Mänskliga indata tränar inte tjänsten, men det kombinerade arbetet från tjänsten och mänskliga granskningsteam gör att utvecklare kan åstadkomma rätt balans mellan effektivitet och noggrannhet. Gransknings verktyget ger också en användarvänlig klient för en rad olika Content Moderators resurser.

![Hemsida för Content Moderator-verktyget för mänsklig granskning](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder Content Moderator-tjänsten känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Komma igång med Content Moderator-tjänsten genom att följa instruktionerna för att [prova Content Moderator på webben](quick-start.md).