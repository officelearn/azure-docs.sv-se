---
title: Vad är Anpassad sökning i Bing?
titlesuffix: Azure Cognitive Services
description: Ger en översikt på hög nivå över anpassad sökning i Bing.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 2483bf36bb18af21bc454e08f3321b33094c43c8
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814312"
---
# <a name="what-is-bing-custom-search"></a>Vad är Anpassad sökning i Bing?

Med anpassad sökning i Bing kan du skapa skräddarsydda sökningar om ämnen som intresserar dig. Om du till exempel äger en webbplats med sökfunktion kan du ange vilka domäner, webbplatser och webbsidor som Bing ska söka i. Användarna ser sökresultat som skräddarsytts efter det innehåll som intresserar dem, i stället för att behöva bläddra bland irrelevanta sökresultat.

Du skapar en anpassad vy av webben med hjälp av [portalen](https://customsearch.ai) för anpassad sökning i Bing. Med portalen kan du skapa en anpassad sökinstans som anger vilka domäner, webbplatser och webbsidor där Bing ska söka, och webbplatser där du inte vill att Bing ska söka. Utöver att specificera webbadresserna för innehåll som du redan känner till, kan du också använda portalen för att hitta relevant innehåll som du kanske vill lägga till.

Portalen ger dig också möjlighet att fästa en specifik webbsida högst upp i sökresultatet om användaren anger ett specifikt sökord. 

När du har definierat din instans kan du integrera anpassad sökning i din webbplats, skrivbordsapp eller mobila app genom att anropa API:et för anpassad sökning. Har du en webbplats eller ett program som är webbaserat kan du låta det värdbaserade användargränssnittet rendera sökgränssnittet åt dig.

Följande bild visar hur enkelt det är att integrera anpassad sökning.

![bild alt](./media/bcs-overview.png "Så fungerar anpassad sökning i Bing.")

## <a name="adding-custom-search-box-suggestions"></a>Lägga till anpassade förslag för sökrutan

Du kan utöka din anpassade sökupplevelse med anpassade förslag för sökrutan. Funktionen gör att du kan erbjuda anpassade sökförslag som är relevanta för din sökupplevelse. När användaren skriver i sökrutan visas listrutan med föreslagna frågesträngar baserat på en del av användarens frågesträng. Du kan ange om du bara vill returnera dina anpassade förslag eller även inkludera Bing-förslag. [Läs mer](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Lägga till anpassad bild i sökupplevelse

Du kan utöka din anpassade sökupplevelse med bilder. På ett liknande sätt som med webbresultat har anpassad sökning stöd för bildsökning i listan över webbplatser i dina instanser. [Läs mer](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Lägga till anpassad video i sökupplevelse

Du kan utöka din anpassade sökupplevelse med videor. På ett liknande sätt som med webbresultat har anpassad sökning stöd för videosökning i listan över webbplatser i dina instanser. [Läs mer](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Dela din anpassade sökinstans med andra

Du kan möjliggöra samarbetsbaserad redigering och testning av din instans genom att helt enkelt dela den med medlemmar i ditt team. [Läs mer](share-your-custom-search.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång snabbt finns i [Create your first Bing Custom Search instance](quick-start.md) (Skapa din första instans för anpassad sökning i Bing).

Mer information om hur du anpassar din sökinstans finns i [Define a custom search instance](define-your-custom-view.md) (Definiera en anpassad Sökinstans).

Bekanta dig med referensinnehållet för var och en av slutpunkterna för anpassad sökning. Referensen innehåller de slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten.

- [API för anpassad sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API för anpassad bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API för anpassad video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API för anpassade automatiska förslag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Se till att läsa [Bing Use and Display Requirements](./use-and-display-requirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.