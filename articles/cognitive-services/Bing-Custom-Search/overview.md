---
title: Vad är Bing anpassad sökning? | Microsoft Docs
description: Innehåller en översikt över Bing anpassad sökning
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352881"
---
# <a name="what-is-bing-custom-search"></a>Vad är Bing anpassad sökning?

Bing anpassad sökning kan du skapa en anpassad sökning upplevelser efter avsnitt som intresserar dig. Till exempel om du har en webbplats som ger en sökinställningar kan kan du ange domäner, webbplatser och webbsidor som Bing söker. Användarna ser sökresultat som är skräddarsydda för de är intresserad av i stället för att innehållet till sidan via sökresultat som har irrelevanta innehåll.

Använd Bing anpassad sökning för att skapa den anpassade vyn för webben [portal](https://customsearch.ai). Portalen kan du skapa en anpassad sökning-instans som anger domäner, webbplatser och webbsidor som du vill använda Bing för att söka och de webbplatser som du inte vill att söka. Ange URL: er för det innehåll som du vet om, kan du också använda portalen för att hitta relevant innehåll som du kanske vill lägga till.

Portalen kan du fästa en specifik webbsida överst i sökresultatet om användaren anger en specifik sökterm. 

När du har definierat din instans kan du integrera anpassad sökning i din webbplats, skrivbordsapp eller mobilapp genom att anropa API: et för anpassad sökning. Om du har en webbaserad webbplats eller program kan låta du värdbaserade Användargränssnittet återge search-gränssnittet för dig.

Följande bild visar enkelhet anpassad sökning-integrering.

![bild alt](./media/bcs-overview.png "hur Bing anpassad sökning fungerar.")

## <a name="customize-search-suggestions"></a>Anpassa sökförslag

Om du prenumererar på anpassad sökning på lämplig nivå (finns i [priser sidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), kan du anpassa sökförslag som gjorts i din upplevelse för anpassad sökning. Anpassad Autosuggest API returnerar en lista över föreslagna frågor baserat på en partiell frågesträng som användaren anger. Med anpassade Autosuggest kan du ange anpassade sökförslag som är relevanta för dina sökinställningar. Du ange om du vill returnera endast anpassade förslag eller att inkludera Bing förslag. Om Bing förslag ingår visas anpassade förslag innan förslag Bing tillhandahåller. Bing förslag är begränsade till kontexten för anpassad sökning-instans.

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång snabbt se [skapa din första Bing anpassad sökning instans](quick-start.md).

Mer information om tillgängliga alternativ för att anpassa search-instansen finns [definiera en anpassad sökning instans](define-your-custom-view.md).

Bekanta dig med de [anpassad sökning API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) referens. Referensen innehåller listan över slutpunkter, rubriker och frågeparametrar som du vill använda för att begära sökresultat. Den omfattar också definitioner av objekt som svar.

Information om hur du anpassar förslag finns [definiera anpassade sökförslag](define-custom-suggestions.md).

Se till att läsa [Bing användas och visa krav](./use-and-display-requirements.md) så att du inte delar någon av reglerna om hur du använder sökresultatet.