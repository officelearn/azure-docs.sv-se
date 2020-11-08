---
title: Vad är API:et för anpassad Bing-sökning?
titleSuffix: Azure Cognitive Services
description: Med API:et för anpassad Bing-sökning kan du skapa skräddarsydda sökningar om ämnen som intresserar dig.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 4490142a278d5e724fade42e697d8cf2206c0da8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367343"
---
# <a name="what-is-the-bing-custom-search-api"></a>Vad är API:et för anpassad Bing-sökning?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Med API:et för anpassad Bing-sökning kan du skapa skräddarsydda reklamfria sökningar om ämnen som intresserar dig. Du kan ange de domäner och webbsidor som Bing ska söka på samt fästa, höja eller sänka specifikt innehåll för att skapa en anpassad vy över webben och hjälpa dina användare att snabbt hitta relevanta sökresultat. 

## <a name="features"></a>Funktioner

|Funktion  |Beskrivning  |
|---------|---------|
|[Anpassade sökförslag i realtid](define-custom-suggestions.md)     | Ge sökförslag som kan visas som en listruta när dina användare skriver.       | 
|[Anpassade funktioner för bildsökning](get-images-from-instance.md)     | Gör så att dina användare kan söka efter bilder från domäner och webbplatser som anges i din anpassade sökinstans.        |        
|[Anpassade funktioner för videosökning](get-videos-from-instance.md)     | Gör så att dina användare kan söka efter videofilmer från domäner och webbplatser som anges i din anpassade sökinstans.        |    
|[Dela din anpassade sökinstans](share-your-custom-search.md)     | Samarbeta för att redigera och testa sökinstansen genom att dela den med teammedlemmarna.        | 
|[Konfigurera ett användargränssnitt för dina program och webbplatser](hosted-ui.md)     | Innehåller ett värdbaserad gränssnitt som du enkelt kan integrera med dina webb sidor och webb program som ett JavaScript-kodfragment.        | 
## <a name="workflow"></a>Arbetsflöde

Du kan skapa en anpassad sökinstans genom att använda [portalen för Anpassad Bing-sökning](https://customsearch.ai). Med portalen kan du skapa en anpassad sökinstans som anger vilka domäner, webbplatser och webbsidor där Bing ska söka, tillsammans med sådana du inte vill att Bing ska söka. Du kan också använda portalen till följande: förhandsgranska sökfunktionerna, justera sökrangordningen som API:et tillhandahåller och, om du vill, konfigurera ett sökbart användargränssnitt som ska återges på webbplatser och i appar.

När du har skapat sökinstansen kan du integrera den (och ett användargränssnitt om du vill) i din webbplats eller app genom att anropa API:et för anpassad Bing-sökning:

![Bild som visar att du kan ansluta till den anpassade Bing-sökningen via API: et](media/BCS-Overview.png "Hur Anpassad sökning i Bing fungerar.")


## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång snabbt finns i [Create your first Bing Custom Search instance](quick-start.md) (Skapa din första instans för anpassad sökning i Bing).

Mer information om hur du anpassar din sökinstans finns i [Define a custom search instance](define-your-custom-view.md) (Definiera en anpassad Sökinstans).

Läs [Användnings- och visningskrav för Bing](../bing-web-search/use-display-requirements.md) för att använda sökresultat i dina tjänster och appar.

Gå till [sidan Bing-sökning API Hub](../bing-web-search/overview.md) och utforska de andra tillgängliga API: erna.

Bekanta dig med referensinnehållet för var och en av slutpunkterna för anpassad sökning. Referensen innehåller de slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API för anpassad sökning](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API för anpassad bild](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API för anpassad video](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API för anpassade automatiska förslag](/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)