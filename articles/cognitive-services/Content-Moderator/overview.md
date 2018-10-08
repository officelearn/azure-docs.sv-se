---
title: Vad är Content Moderator?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder Content Moderator för att spåra, flagga, utvärdera och filtrera olämpligt innehåll i användargenererat innehåll.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: e109376f47d921fb18d7bb9a6252e80315419ec0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226049"
---
# <a name="what-is-content-moderator"></a>Vad är Content Moderator?

Innehållsmoderering är en process för övervakning av potentiellt stötande, oönskat och riskfyllt innehåll. Modererat innehåll kan vara bilder, text och videor.

## <a name="where-it-is-used"></a>Var verktyget kan användas

Följande lista innehåller några exempel på scenarier där Content Moderator kan användas:

- Marknadsplatser online som modererar produktkataloger och användargenererat innehåll
- Spelföretag som modererar användargenererade spelartefakter och chattrum
- Plattformar för sociala meddelanden som modererar bilder, text och videor som läggs till av användarna
- Medieföretag som implementerar centraliserad innehållsmoderering för deras innehåll
- K-12-utbildningslösningsföretag som filtrerar bort olämpligt och stötande innehåll för elever och lärare

## <a name="what-it-includes"></a>Vad verktyget innehåller

Content Moderator består av flera webbtjänst-API:er och ett inbyggt HITL-granskningsverktyg (Human-In-The-Loop) som hjälper dig att moderera bilder, text och videor.

![Content Moderator-blockdiagram](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API:er

Content Moderator innehåller följande API:er:
  - [**API för textmoderering**](text-moderation-api.md): Använd det här API:et för att söka igenom text efter svordomar, olämpliga ord och stötande och personligt identifierbar information (PII).
  - [**API för anpassade termlistor**](try-terms-list-api.md): Använd det här API:et för att matcha mot anpassade listor med termer utöver de inbyggda termerna. Använd dessa listor för att blockera eller tillåta innehåll baserat på dina innehållsprinciper.  
  - [**API för bildmoderering**](image-moderation-api.md): Använd det här API:et för att söka igenom bilder efter olämpligt innehåll, för att identifiera text i bilder med funktionen för optisk teckenläsning (OCR) och för att identifiera ansikten.
  - [**API för anpassade bildlistor**](try-image-list-api.md): Använd det här API:et för att matcha mot anpassade listor med bilder och i förväg identifierat innehåll som du inte behöver klassificera igen.
  - [**API för videomoderering**](video-moderation-api.md): Använd det här API:et för att söka igenom videor efter olämpligt innehåll.
  - [**Gransknings-API:er**](try-review-api-job.md): Använd åtgärderna [Jobb](try-review-api-job.md), [Granskningar](try-review-api-review.md) och [Arbetsflöde](try-review-api-workflow.md) för att skapa och automatisera HITL-arbetsflöden (Human-In-The-Loop) i granskningsverktyget.

## <a name="human-review-tool"></a>Verktyg för mänsklig granskning

Det inbyggda [verktyget för mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md) ingår i din Content Moderator-prenumeration. Använd det tidigare nämnda gransknings-API:et för att skapa granskningar eller omdömen av text, bilder och videor som hjälper dina mänskliga moderatorer att fatta det slutliga beslutet.

![Content Moderator-videogranskningsverktyget](images/video-review-default-view.png)

## <a name="next-steps"></a>Nästa steg

Använd [snabbstarten](quick-start.md) för att komma igång med Content Moderator.
