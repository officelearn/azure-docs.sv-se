---
title: Vad är Anpassad sökning i Bing? | Microsoft Docs
description: Innehåller en översikt över Bing Custom Search
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: b6f50844d6571cca6d63c1db7a85863e3d22d411
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948085"
---
# <a name="what-is-bing-custom-search"></a>Vad är Anpassad sökning i Bing?

Bing Custom Search kan du skapa skräddarsydda sökmöjligheter för ämnen som intresserar dig. Till exempel, om du äger en webbplats som ger en sökfunktion kan kan du ange de domäner, webbplatser och webbsidor som söker i Bing. Användarna ser sökresultat som skräddarsys för det innehåll som de bryr dig om slipper att bläddra igenom sökresultat som har inte relevant innehåll.

Använd Bing Custom Search för att skapa den anpassade vyn webb-, [portal](https://customsearch.ai). Portalen kan du skapa en anpassad Sökinstans som anger domänerna, webbplatser och webbsidor som du vill att Bing för att söka och vilka webbplatser som du inte vill att söka. Förutom att ange URL: er för det innehåll som du vet om använda du också portalen för att hitta relevant innehåll som du kanske vill lägga till.

Portalen kan du fästa en specifik webbsida längst upp i sökresultatet om användaren anger ett specifikt sökord. 

När du har definierat din instans integrera du anpassad sökning i din webbplats, skrivbordsapp eller mobila appar genom att anropa API: et för anpassad sökning. Om du har en webbaserad webbplats eller ett program kan låta du värdbaserade Användargränssnittet rendera gränssnittet search åt dig.

Följande bild visar enkelheten med anpassad sökning i integrationen.

![bild alt](./media/bcs-overview.png "hur Bing Custom Search fungerar.")

## <a name="adding-custom-search-box-suggestions"></a>Att lägga till anpassade box sökförslag

Du kan utöka din anpassade sökupplevelse med anpassad sökning i rutan förslag. Den här funktionen kan du tillhandahålla anpassade sökförslag som är relevanta för din sökupplevelse. När användaren skriver i sökrutan i listrutan innehåller föreslagna frågesträngar baserat på användarens partiella frågesträngen. Du kan ange om du vill returnera endast dina anpassade förslag eller också innehålla förslag för Bing. [Läs mer](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Sökfunktion för att lägga till anpassad avbildning

Du kan utöka din anpassade sökupplevelse med bilder. Liknar Webbresultat, har stöd för anpassad sökning söker efter bilder i din instans listan över webbplatser. [Läs mer](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Att lägga till anpassade video sökupplevelse

Du kan utöka din anpassade sökupplevelse med videor. Liknar Webbresultat, har stöd för anpassad sökning söker efter videor i din instans listan över webbplatser. [Läs mer](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Dela din anpassade Sökinstans med andra

Du kan enkelt samarbetsfunktioner redigering och testning av din instans genom att dela den med medlemmar i ditt team. [Läs mer](share-your-custom-search.md).

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt, se [skapar din första Bing Custom Search-instans](quick-start.md).

Mer information om hur du anpassar din search-instans finns i [definiera en anpassad Sökinstans](define-your-custom-view.md).

Bekanta dig med referensinnehållet för var och en av slutpunkterna för anpassad sökning. Referensen innehåller slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten.

- [API för anpassad sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API för anpassad avbildning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Anpassade Vidoe API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Anpassade automatiska förslag API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Se till att läsa [Bing Use and Display Requirements](./use-and-display-requirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.