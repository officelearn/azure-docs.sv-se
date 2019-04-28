---
title: Vad är API:et för anpassad Bing-sökning?
titlesuffix: Azure Cognitive Services
description: Med API:et för anpassad Bing-sökning kan du skapa skräddarsydda sökningar om ämnen som intresserar dig.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 03/4/2019
ms.author: aahi
ms.openlocfilehash: e788da047cb0567fc00f27130621a2f21e575dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335538"
---
# <a name="what-is-the-bing-custom-search-api"></a>Vad är API:et för anpassad Bing-sökning?

Med API:et för anpassad Bing-sökning kan du skapa skräddarsydda reklamfria sökningar om ämnen som intresserar dig. Du kan ange de domäner och webbsidor som Bing ska söka på samt fästa, höja eller sänka specifikt innehåll för att skapa en anpassad vy över webben och hjälpa dina användare att snabbt hitta relevanta sökresultat. 

## <a name="features"></a>Funktioner

|Funktion  |Beskrivning  |
|---------|---------|
|[Anpassade sökförslag i realtid](define-custom-suggestions.md)     | Ge sökförslag som kan visas som en listruta när dina användare skriver.       | 
|[Anpassade funktioner för bildsökning](get-images-from-instance.md)     | Gör så att dina användare kan söka efter bilder från domäner och webbplatser som anges i din anpassade sökinstans.        |        
|[Anpassade funktioner för videosökning](get-videos-from-instance.md)     | Gör så att dina användare kan söka efter videofilmer från domäner och webbplatser som anges i din anpassade sökinstans.        |    
|[Dela din anpassade sökinstans](share-your-custom-search.md)     | Samarbeta för att redigera och testa sökinstansen genom att dela den med teammedlemmarna.        | 
|[Konfigurera ett användargränssnitt för dina program och webbplatser](hosted-ui.md)     | Samarbeta för att redigera och testa sökinstansen genom att dela den med teammedlemmarna.        | 
## <a name="workflow"></a>Arbetsflöde

Du kan skapa en anpassad sökinstans genom att använda [portalen för Anpassad Bing-sökning](https://customsearch.ai). Med portalen kan du skapa en anpassad sökinstans som anger vilka domäner, webbplatser och webbsidor där Bing ska söka, tillsammans med sådana du inte vill att Bing ska söka. Du kan också använda portalen till följande: förhandsgranska sökfunktionerna, justera sökrangordningen som API:et tillhandahåller och, om du vill, konfigurera ett sökbart användargränssnitt som ska återges på webbplatser och i appar.

När du har skapat sökinstansen kan du integrera den (och ett användargränssnitt om du vill) i din webbplats eller app genom att anropa API:et för anpassad Bing-sökning:

![Bild som visar att du kan ansluta till anpassad Bing-sökning via API:et](media/BCS-Overview.png "Så här fungerar anpassad Bing-sökning.")


## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång snabbt finns i [Create your first Bing Custom Search instance](quick-start.md) (Skapa din första instans för anpassad sökning i Bing).

Mer information om hur du anpassar din sökinstans finns i [Define a custom search instance](define-your-custom-view.md) (Definiera en anpassad Sökinstans).

Läs [Användnings- och visningskrav för Bing](./use-and-display-requirements.md) för att använda sökresultat i dina tjänster och appar.

Bekanta dig med referensinnehållet för var och en av slutpunkterna för anpassad sökning. Referensen innehåller de slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten.

- [API för anpassad sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API för anpassad bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API för anpassad video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API för anpassade automatiska förslag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)

