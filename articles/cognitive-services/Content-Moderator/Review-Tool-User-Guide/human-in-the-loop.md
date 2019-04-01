---
title: Lär dig granska verktyget begrepp - Content Moderator
titlesuffix: Azure Cognitive Services
description: Läs mer om Content Moderator granskar-verktyg, en webbplats som samordnar en kombinerad AI och mänsklig granskning moderering arbete.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755890"
---
# <a name="content-moderator-review-tool"></a>Content Moderator-granskningsverktyget

Azure Content Moderator tillhandahåller tjänster för att kombinera machine learning-innehållsmoderering med mänsklig granskning och [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) webbplats är en användarvänlig klientdel som ger detaljerad åtkomst till dessa tjänster.

![Granska verktyget instrumentpanelen i en webbläsare](./images/0-dashboard.png)

## <a name="what-it-does"></a>Vad läget gör

Den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com), när de används tillsammans med datorstödd moderering API: er, kan du utföra följande uppgifter i innehållsmoderering processen:

- Använda en uppsättning verktyg för att hantera innehåll i flera format (text, bild och video).
- Automatisera skapandet av mänskliga [granskar](../review-api.md#reviews) när moderering API resulterar kommer i.
- Tilldela eller eskalera innehåll granskningar till flera granska team, ordnade i nivåer för innehåll kategori eller upplevelse.
- Använd standard eller anpassad logik filter ([arbetsflöden](../review-api.md#workflows)) att sortera och spåra innehåll, utan att behöva skriva någon kod.
- Använd [kopplingar](./configure.md#connectors) bearbetning av innehåll med Microsoft PhotoDNA, textanalys och Ansikts-API: er förutom Moderator-API: er för innehåll.
- Skapa din egen anslutning för att skapa arbetsflöden för alla API: er eller affärsprocess.
- Få viktiga prestandamått innehållsmoderering-processer.

## <a name="review-tool-dashboard"></a>Granska verktyget instrumentpanel

På den **instrumentpanelen** fliken som du kan se viktiga mått för innehåll granskning görs verktyget. Se hur många av totalen, klar och väntar på granskning av bild, text och videoinnehåll. Du kan också se detaljer för användare och team som har slutfört granskningar, samt moderering-taggar som har tillämpats.

![Visa instrumentpanelen](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Granska verktyget autentiseringsuppgifter

När du registrerar dig med den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com), uppmanas du att välja en Azure-region för du kontot. Detta beror på den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnadsfri utvärderingsversion nyckel för Azure Content Moderator-tjänster; du behöver den här nyckeln för åtkomst till alla tjänster från en REST-anrop eller klient-SDK. Du kan visa din nyckel och API: et slutpunkts-URL genom att välja **inställningar** > **autentiseringsuppgifter**.

![Content Moderator-autentiseringsuppgifter](images/settings-6-credentials.png)

## <a name="next-steps"></a>Nästa steg

Se [konfigurera granskningsverktyget](./configure.md) och lär dig att komma åt granska verktyget resurser och ändra inställningarna.