---
title: Vad är Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder Content Moderator för att spåra, flagga, utvärdera och filtrera olämpligt material i användargenererat innehåll.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 10/05/2018
ms.author: sajagtap
ms.openlocfilehash: 5756e8fb451b073c68271359848ab27373ad85ed
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309560"
---
# <a name="what-is-content-moderator"></a>Vad är Content Moderator?

Innehållsmoderering är processen för att övervaka innehåll med text, bilder eller video efter material som är potentiellt stötande, oönskat eller riskfyllt. Flaggat innehåll kan sedan döljas eller på annat sätt åtgärdas för att uppfylla krav eller upprätthålla den avsedda miljön för användare.

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

### <a name="apis"></a>API:er

Content Moderator-tjänsten innehåller följande API:er:
  - [**API för textmoderering**](text-moderation-api.md): Använd det här API:et för att söka igenom text efter svordomar, olämpliga ord och stötande och personligt identifierbar information (PII).
  - [**API för anpassade termlistor**](try-terms-list-api.md): Använd det här API:et för att matcha mot anpassade listor med termer utöver de inbyggda termerna. Använd dessa listor för att blockera eller tillåta innehåll baserat på dina innehållsprinciper.  
  - [**API för bildmoderering**](image-moderation-api.md): Använd det här API:et för att söka igenom bilder efter olämpligt innehåll, för att identifiera text i bilder med funktionen för optisk teckenläsning (OCR) och för att identifiera ansikten.
  - [**API för anpassade bildlistor**](try-image-list-api.md): Använd det här API:et för att matcha mot anpassade listor med bilder och i förväg identifierat innehåll som du inte behöver klassificera igen.
  - [**API för videomoderering**](video-moderation-api.md): Använd det här API:et för att söka igenom videor efter olämpligt innehåll.
  - [**Gransknings-API:er**](try-review-api-job.md): Använd åtgärderna [Jobb](try-review-api-job.md), [Granskningar](try-review-api-review.md) och [Arbetsflöde](try-review-api-workflow.md) för att skapa och automatisera HITL-arbetsflöden (Human-In-The-Loop) i granskningsverktyget.

### <a name="human-review-tool"></a>Verktyg för mänsklig granskning

Det inbyggda [verktyget för mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md) ingår i din Content Moderator-prenumeration. Använd det tidigare nämnda gransknings-API:et för att skapa granskningar eller omdömen av text, bilder och videor som hjälper dina mänskliga moderatorer att fatta det slutliga beslutet.

![Content Moderator-videogranskningsverktyget](images/video-review-default-view.png)

## <a name="next-steps"></a>Nästa steg

Använd [snabbstarten](quick-start.md) för att komma igång med Content Moderator.
