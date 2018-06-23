---
title: Översikt över Azure innehåll kontrollant | Microsoft Docs
description: Lär dig hur du använder kontrollant innehåll att spåra flaggan, utvärdera och filtrera olämpligt innehåll i innehåll som skapats av användaren.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355143"
---
# <a name="what-is-content-moderator"></a>Vad är innehåll kontrollant?

Innehållet inte är processen att övervaka för möjliga stötande oönskade och riskfyllda innehåll. Kontrollerade innehåll kan vara bilder, text och videor.

## <a name="where-it-is-used"></a>Där den används

I följande lista visar några exempelscenarier där innehåll kontrollant används:

- Onlinemarknadsplatser Kontrollera produktkataloger och användargenererat innehåll
- Spel företag kontrollera användaren genererade spel artefakter och chatt-rum
- Sociala plattform för meddelandetjänster Kontrollera bilder, text och videor som lagts till av sina användare
- Enterprise media företag som implementerar centraliserad innehåll avbrottsmoderering för deras innehåll
- Leverantör av K-12 education filtrering felaktiga och stötande innehåll för elever och lärare

## <a name="what-it-includes"></a>Den innehåller

Kontrollant består av flera web service API: er och en inbyggd hr-i-the-loop innehållsgranskning verktyg som hjälper måttlig bilder, text och videor.

![Innehåll kontrollant blockdiagram](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API:er

Innehåll kontrollant innehåller följande API: er:
  - [**Text avbrottsmoderering API**](text-moderation-api.md): Använd den här API för att skanna text möjliga svordomar explicit, tyder, stötande och personligt identifierbar information (PII).
  - [**Anpassade termen listan API**](try-terms-list-api.md): Använd detta API för matchning mot anpassade listor termer förutom inbyggda villkoren. Använd dessa listor för att blockera eller tillåta innehåll enligt dina principer för innehåll.  
  - [**Bild av avbrottsmoderering API**](image-moderation-api.md): Använd den här API för att skanna bilder för innehåll för vuxna och dyr, identifiera text i bilder med funktioner som OCR (OCR) och identifiera ytor.
  - [**Anpassade bildlista API**](try-image-list-api.md): Använd den här API för matchning mot anpassade listor med bilder, före identifierade innehåll som du inte behöver klassificera igen.
  - [**Video avbrottsmoderering API**](video-moderation-api.md): Använd den här API: et för att skanna videor för potentiella innehåll för vuxna och dyr.
  - [**Granska API: er**](try-review-api-job.md): Använd den [jobb](try-review-api-job.md), [granskningar](try-review-api-review.md), och [arbetsflöde](try-review-api-workflow.md) åtgärder för att skapa och automatisera hr-i-the-loop arbetsflöden inom Granska-verktyget.

## <a name="human-review-tool"></a>Verktyg för mänsklig granskning

Prenumerationen innehåll kontrollant innehåller inbyggt [mänsklig granska verktyget](Review-Tool-User-Guide/human-in-the-loop.md). Använd ovanstående granska API för att skapa granskning av text, bilder och videor för din mänsklig moderatorer att fatta slutliga beslut.

![Innehåll kontrollant video granska verktyget](images/video-review-default-view.png)

## <a name="next-steps"></a>Nästa steg

Använd den [Quickstart](quick-start.md) att komma igång med innehåll kontrollanten.
