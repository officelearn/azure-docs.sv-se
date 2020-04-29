---
title: Lär dig att granska verktygs koncept – Content Moderator
titleSuffix: Azure Cognitive Services
description: Lär dig mer om Content Moderator gransknings verktyget, en webbplats som samordnar en kombinerad AI-och mänsklig gransknings ansträngning.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76169508"
---
# <a name="content-moderator-review-tool"></a>Content Moderator gransknings verktyget

Azure Content Moderator tillhandahåller tjänster för att kombinera maskin inlärnings innehåll med mänsklig granskning och webbplatsen för [gransknings verktyg](https://contentmoderator.cognitive.microsoft.com) är en användarvänlig klient del som ger detaljerad åtkomst till dessa tjänster.

![Instrument panelen för gransknings verktyg i en webbläsare](./images/0-dashboard.png)

## <a name="what-it-does"></a>Vad läget gör

I [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com), när det används tillsammans med de verktyg för redigering av datorbaserade redigerings program, kan du utföra följande uppgifter i innehålls moderator processen:

- Använd en uppsättning verktyg för att måttligt innehåll i flera format (text, bild och video).
- Automatisera skapandet av mänsklig [granskningar](../review-api.md#reviews) när du får kontroll-API-resultat.
- Tilldela eller eskalera innehålls granskningar till flera gransknings grupper, sorterade efter innehålls kategori eller miljö nivå.
- Använd standard-eller anpassade logik filter ([arbets flöden](../review-api.md#workflows)) för att sortera och spåra innehåll utan att skriva någon kod.
- Använd [kopplingar](./configure.md#connectors) för att bearbeta innehåll med Microsoft PhotoDNA, textanalys och ansikts tjänster utöver Content moderator-API: er.
- Skapa en egen anslutning för att skapa arbets flöden för alla API: er och affärs processer.
- Hämta viktiga prestanda mått för dina innehålls moderator processer.

## <a name="review-tool-dashboard"></a>Instrument panel för gransknings verktyg

På fliken **instrument panel** kan du se viktiga mått för innehålls granskningar som har gjorts i verktyget. Se antalet totala, fullständiga och väntande recensioner för bild-, text-och video innehåll. Du kan också se en nedbrytning av användare och team som har slutfört granskningar, samt vilka redigerings etiketter som har tillämpats.

![Visa instrument panel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Granska autentiseringsuppgifterna för verktyget

När du registrerar dig med [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com)uppmanas du att välja en Azure-region för ditt konto. Detta beror på att [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnads fri utvärderings nyckel för Azure Content moderator Services; du behöver den här nyckeln för att få åtkomst till alla tjänster från ett REST-anrop eller klient-SDK. Du kan visa URL: en för din nyckel och API-slutpunkt genom att välja **Inställningar** > **autentiseringsuppgifter**.

![Content Moderator autentiseringsuppgifter](images/settings-6-credentials.png)

## <a name="next-steps"></a>Nästa steg

Se [Konfigurera gransknings verktyget](./configure.md) för att lära dig att komma åt resurser för gransknings verktyget och ändra inställningar.