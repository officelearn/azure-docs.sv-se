---
title: Läs verktygskoncept för granskning – Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Läs mer om verktyget Granskning av innehållsmoderatorer, en webbplats som samordnar en kombinerad AI- och mänsklig granskningsmodering.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169508"
---
# <a name="content-moderator-review-tool"></a>Granskningsverktyg för innehållsmodererator

Azure Content Moderator tillhandahåller tjänster för att kombinera moderering av maskininlärningsinnehåll med mänskliga granskningar, och webbplatsen [för granskningsverktyg](https://contentmoderator.cognitive.microsoft.com) är en användarvänlig klientdel som ger detaljerad åtkomst till dessa tjänster.

![Instrumentpanelen för granskningsverktyg i en webbläsare](./images/0-dashboard.png)

## <a name="what-it-does"></a>Vad läget gör

Med [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com)kan du utföra följande uppgifter i processen för moderering av innehåll när det används tillsammans med API:er för moderering av datorstödd:

- Använd en uppsättning verktyg för att moderera innehåll i flera format (text, bild och video).
- Automatisera skapandet av mänskliga [recensioner](../review-api.md#reviews) när moderering API-resultat kommer in.
- Tilldela eller eskalera innehållsgranskningar till flera granskningsteam, ordnade efter innehållskategori eller upplevelsenivå.
- Använd standard- eller anpassade logikfilter[(arbetsflöden)](../review-api.md#workflows)för att sortera och spåra innehåll utan att skriva någon kod.
- Använd [anslutningsappar](./configure.md#connectors) för att bearbeta innehåll med Microsoft PhotoDNA-, Textanalys- och Face-tjänster utöver API:erna för innehållsmodererator.
- Skapa en egen anslutningsapp för att skapa arbetsflöden för alla API- eller affärsprocesser.
- Få nyckeltal för dina processer för innehållsmoderering.

## <a name="review-tool-dashboard"></a>Instrumentpanel för granskningsverktyg

På fliken **Instrumentpanel** kan du se viktiga mått för innehållsgranskningar som gjorts i verktyget. Se antalet totalt, fullständiga och väntande granskningar för bild-, text- och videoinnehåll. Du kan också se fördelningen av användare och team som har slutfört granskningar, samt de modereringstaggar som har tillämpats.

![Visa instrumentpanel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Autentiseringsuppgifter för granskningsverktyg

När du registrerar dig med [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com)uppmanas du att välja en Azure-region för ditt konto. Detta beror på [att granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnadsfri utvärderingsversion för Azure Content Moderator-tjänster. Du behöver den här nyckeln för att komma åt någon av tjänsterna från ett REST-samtal eller klient SDK. Du kan visa url:en för nyckel- och API-slutpunkt genom att välja **Inställningar** > **.**

![Autentiseringsuppgifter för innehållsmodererator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Nästa steg

Se [Konfigurera granskningsverktyget](./configure.md) om du vill lära dig hur du kommer åt granskningsverktygsresurser och ändrar inställningar.