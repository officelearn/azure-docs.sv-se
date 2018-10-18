---
title: Vad är API för visuellt innehåll?
titlesuffix: Azure Cognitive Services
description: Via tjänsten Visuellt innehåll har utvecklare tillgång till avancerade algoritmer för bearbetning av bilder och returnering av information.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.openlocfilehash: 03cf2fab1200cd617f456a6fcfb9067673d01a19
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340910"
---
# <a name="what-is-computer-vision"></a>Vad är visuellt innehåll?

Den molnbaserade tjänsten för visuellt innehåll ger utvecklare åtkomst till avancerade algoritmer för bearbetning av bilder och returnering av information. Visuellt innehåll fungerar med populära bildformat som JPEG och PNG. Om du vill analysera en bild kan du antingen ladda upp den eller ange en bild-URL. Algoritmer för visuellt innehåll kan analysera innehållet i en bild på olika sätt beroende på de visuella egenskaper som du är intresserad av. Till exempel kan visuellt innehåll avgöra om en bild innehåller stötande eller olämpligt innehåll, eller hitta alla ansikten i en bild.

Du kan använda visuellt innehåll i ditt program genom att antingen använda våra [klientbibliotek](quickstarts-sdk/csharp-analyze-sdk.md) för att anropa tjänsten, eller anropa [REST API](vision-api-how-to-topics/howtocallvisionapi.md) direkt, för att:

- [Analysera bilder för att få information](#analyzing-images-for-insight)
- [Extrahera text från bilder](#extracting-text-from-images)
- [Moderera innehåll i bilder](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>Analysera bilder för att få information

Du kan analysera bilder med hjälp av visuellt innehåll för att identifiera och tillhandahålla insikter om de visuella egenskaper och kännetecken som dina bilder har. Du kan antingen ladda upp innehållet i en bild för att analysera lokala bilder eller ange en webbadress till en bild för att analysera bilder som du inte lagrar lokalt.

Visuellt innehåll kan utföra följande åtgärder när du analyserar en bild:

| Åtgärd | Beskrivning |
| ------ | ----------- |
|**[Tagga visuella egenskaper](concept-tagging-images.md)**|Identifiera och tagga visuella egenskaper i en bild som baseras på över 2 000 identifierbara objekt, levande varelser, landskap och åtgärder. I de fall där taggarna är tvetydiga eller dess betydelse inte är allmän kännedom ger svaren ”ledtrådar” för att tydliggöra taggarnas betydelse i en viss kontext. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.|
|**[Kategorisera en bild](concept-categorizing-images.md)**|Identifiera och kategorisera en hel bild med hjälp av en [kategoritaxonomi](Category-Taxonomy.md) med överordnade/underordnade ärftliga hierarkier. Kategorier kan användas fristående eller med våra nya taggningsmodeller.<br/>Engelska är för närvarande det enda språket som stöds för att tagga och kategorisera bilder.|
|**[Beskriv en bild](concept-describing-images.md)**|Skapa en beskrivning av en hel bild i läsbart språk med fullständiga meningar. Algoritmer för visuellt innehåll genererar olika beskrivningar som baseras på de objekt som identifierats i bilden. Beskrivningarna utvärderas och förtroendepoäng genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng.<br/>Du kan hitta ett exempel på en robot som använder den här tekniken för att skapa bildtexter [på GitHub](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).|
|**[Identifiera ansikten](concept-detecting-faces.md)** |Identifiera ansikten i en bild och ange information om varje identifierat ansikte. Visuellt innehåll returnerar koordinater, rektanglar, kön och ålder för varje identifierat ansikte.<br/>Visuellt innehåll tillhandahåller en delmängd av de funktioner som finns i [Ansiktsigenkänning](/azure/cognitive-services/face/) och du kan använda tjänsten för ansiktsigenkänning för mer detaljerad analys, till exempel identifiering av ansikten och posering.|
|**[Identifiera bildtyper](concept-detecting-image-types.md)**|Identifiera olika kännetecken om en bild som t. ex. om det är en teckning eller om den kan vara ClipArt.|
|**[Identifiera domänspecifikt innehåll](concept-detecting-domain-content.md)**|Använd domänmodeller för att upptäcka och identifiera domänspecifikt innehåll i en bild, till exempel kändisar och landmärken. Om en bild t. ex. innehåller personer kan visuellt innehåll använda en domänmodell för kändisar som ingår i tjänsten för att avgöra om de personer som identifieras i bilden matchar kändisar.|
|**[Identifiera färgschema](concept-detecting-color-schemes.md)**|Analysera användningen av färg i en bild. Visuellt innehåll kan avgöra om en bild är svartvit eller i färg samt identifiera vilka färger som är basfärger och vilka färger som är accentfärger i färgbilder.|
|**[Skapa en miniatyrbild](concept-generating-thumbnails.md)**|Analysera innehållet i en bild för att generera en lämplig miniatyrbild för den. Visuellt innehåll genererar först en högkvalitativ miniatyr och sedan analyseras objekten i bilden för att fastställa *det intressanta området* (ROI). Visuellt innehåll beskär bilden för att passa kraven för ”det intressanta området” (ROI). Den genererade miniatyrbilden kan vid behov anges med proportioner som skiljer sig från proportionerna på den ursprungliga bilden.|

## <a name="extracting-text-from-images"></a>Extrahera text från bilder

Du kan använda visuellt innehåll för att [extrahera text med OCR](concept-extracting-text-ocr.md) från en bild till en maskinläsningsbar teckenström. Om det behövs korrigerar OCR rotationen av den tolkade texten i grader kring den vågräta bildaxeln och anger ramkoordinater för varje ord. OCR har stöd för 25 olika språk och identifierar automatiskt språket för den extraherade texten.

Du kan även [identifiera utskriven och handskriven text](concept-recognizing-text.md) från en bild. Visuellt innehåll kan identifiera och extrahera utskriven och handskriven text från bilder från olika objekt med olika ytor och bakgrunder, till exempel kvitton, affischer, visitkort, brev och whiteboardtavlor. För närvarande finns funktionerna för igenkänning av utskriven och handskriven text i en förhandsversion och engelska är det enda språket som stöds.  

## <a name="moderating-content-in-images"></a>Moderera innehåll i bilder

Du kan använda visuellt innehåll för att [identifiera stötande eller olämpligt innehåll](concept-detecting-adult-content.md) i en bild och ge ett omdöme om sannolikheten att bilden innehåller stötande eller olämpligt innehåll samt generera förtroendepoäng för båda. Filtret för stötande och olämpligt innehåll kan ställas in på en skala för att passa dina preferenser.

## <a name="image-requirements"></a>Avbildningskrav

Visuellt innehåll kan analysera bilder som uppfyller följande krav:

- Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Bildens dimensioner måste vara större än 50 x 50 bildpunkter  
  För OCR måste bildens mått vara mellan 50 x 50 och 4 200 x 4 200 bildpunkter

## <a name="next-steps"></a>Nästa steg

Kom igång med visuellt innehåll med någon av våra snabbstarter:

- [Analysera en bild](quickstarts-sdk/csharp-analyze-sdk.md)
- [Extrahera handskriven text](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Skapa en miniatyrbild](quickstarts-sdk/csharp-thumb-sdk.md)
