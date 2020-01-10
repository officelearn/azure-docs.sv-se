---
title: Vad är visuellt innehåll? - Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Via tjänsten Visuellt innehåll har utvecklare tillgång till avancerade algoritmer för bearbetning av bilder och returnering av information.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b80e09f086cfaa90174dc9abc5a5cd06294036e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770432"
---
# <a name="what-is-computer-vision"></a>Vad är visuellt innehåll?

Azures Visuellt innehåll tjänst ger utvecklare till gång till avancerade algoritmer som bearbetar bilder och retur information, beroende på vilka visuella funktioner du är intresse rad av. Visuellt innehåll kan till exempel avgöra om en bild innehåller innehåll som är olämpligt för barn eller om den kan hitta alla mänskliga ansikten i en bild.

Du kan använda Visuellt innehåll i ditt program via ett inbyggt SDK eller genom att anropa REST API direkt. Den här sidan beskriver allmänt vad du kan göra med Visuellt innehåll.

## <a name="computer-vision-for-digital-asset-management"></a>Visuellt innehåll för digital till gångs hantering

Visuellt innehåll kan sätta många moder scenarier för Digital Asset Management (damm). DAMM är affärs processen att organisera, lagra och hämta omfattande medie till gångar och hantera digitala rättigheter och behörigheter. Ett företag kan till exempel vilja gruppera och identifiera bilder baserat på synliga logo typer, ansikten, objekt, färger och så vidare. Eller så kanske du vill [Generera bild texter automatiskt för bilder](./Tutorials/storage-lab-tutorial.md) och bifoga nyckelord så att de är sökbara. För en allt-i-ett-damm-lösning med hjälp av Cognitive Services, Azure Kognitiv sökning och intelligent rapportering, se [vägledningen för Knowledge utvinnings Accelerator](https://github.com/Azure-Samples/azure-search-knowledge-mining) på GitHub. Andra moder exempel finns i lagrings platsen för [visuellt innehåll Solution templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="analyze-images-for-insight"></a>Analysera bilder för att få insikter

Du kan analysera bilder för att identifiera och få insikter om bildernas visuella egenskaper och kännetecken. Alla funktioner i tabellen nedan tillhandahålls av [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)-API:et.

| Åtgärd | Beskrivning |
| ------ | ----------- |
|**[Tagga visuella egenskaper](concept-tagging-images.md)**|Identifiera och tagga visuella egenskaper i en bild, från tusentals identifierbara objekt, levande varelser, landskap och åtgärder. När taggarna är tvetydiga eller inte vanliga kunskaper innehåller API-svaret tips för att klargöra taggens kontext. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.|
|**[Upptäcka objekt](concept-object-detection.md)**| Objektidentifiering liknar taggar, men API:n returnerar avgränsningsfältets koordinater för varje tagg som tillämpas. Om en bild exempelvis innehåller en hund, en katt och en person, kommer identifieringsåtgärden visa en lista över dessa objekt tillsammans med deras koordinater i bilden. Du kan använda den här funktionen till att bearbeta ytterligare relationer mellan objekt i en bild. Du får även veta när det finns flera instanser av samma tagg i en bild.|
|**[Identifiera varumärken](concept-brand-detection.md)**|Identifiera varumärken i bilder och videor från en databas med tusentals olika globala logotyper. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering.|
|**[Kategorisera en bild](concept-categorizing-images.md)**|Identifiera och kategorisera en hel bild med hjälp av en [kategoritaxonomi](Category-Taxonomy.md) med överordnade/underordnade ärftliga hierarkier. Kategorier kan användas fristående eller med våra nya taggningsmodeller.<br/>Engelska är för närvarande det enda språket som stöds för att tagga och kategorisera bilder.|
|**[Beskriv en bild](concept-describing-images.md)**|Skapa en beskrivning av en hel bild i läsbart språk med fullständiga meningar. Algoritmer för visuellt innehåll genererar olika beskrivningar som baseras på de objekt som identifierats i bilden. Beskrivningarna utvärderas och förtroendepoäng genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng.|
|**[Identifiera ansikten](concept-detecting-faces.md)** |Identifiera ansikten i en bild och ange information om varje identifierat ansikte. Visuellt innehåll returnerar koordinater, rektanglar, kön och ålder för varje identifierat ansikte.<br/>Visuellt innehåll tillhandahåller en delmängd av funktionerna för [ansikts](/azure/cognitive-services/face/) tjänster. Du kan använda ansikts tjänsten för mer detaljerad analys, t. ex. ansikts identifiering och identifiering.|
|**[Identifiera bildtyper](concept-detecting-image-types.md)**|Identifiera olika kännetecken om en bild som t. ex. om det är en teckning eller om den kan vara ClipArt.|
|**[Identifiera domänspecifikt innehåll](concept-detecting-domain-content.md)**|Använd domänmodeller för att upptäcka och identifiera domänspecifikt innehåll i en bild, till exempel kändisar och landmärken. Om en bild till exempel innehåller personer kan Visuellt innehåll använda en domän modell för kändisar för att avgöra om de personer som identifieras i avbildningen är kända kändisar.|
|**[Identifiera färgschema](concept-detecting-color-schemes.md)**|Analysera användningen av färg i en bild. Visuellt innehåll kan avgöra om en bild är svartvit eller i färg samt identifiera vilka färger som är basfärger och vilka färger som är accentfärger i färgbilder.|
|**[Skapa en miniatyrbild](concept-generating-thumbnails.md)**|Analysera innehållet i en bild för att generera en lämplig miniatyrbild för den. Visuellt innehåll genererar först en högkvalitativ miniatyr och sedan analyseras objekten i bilden för att fastställa *intresseområdet*. Visuellt innehåll beskär sedan bilden för att den ska uppfylla kraven för intresseområdet. Den genererade miniatyrbilden kan vid behov anges med proportioner som skiljer sig från proportionerna på den ursprungliga bilden.|
|**[Hämta ett intresseområde](concept-generating-thumbnails.md#area-of-interest)**|Analysera innehållet i en bild för att returnera koordinaterna för *intresseområdet*. I stället för att beskära bilden och generera en miniatyr, returnerar Visuellt innehåll områdets avgränsnings Rams koordinater, så det anropande programmet kan ändra den ursprungliga avbildningen efter behov.|

## <a name="extract-text-from-images"></a>Extrahera text från bilder

Du kan använda Visuellt innehåll [Read](concept-recognizing-text.md#read-api) API för att extrahera tryckt och handskriven text från bilder till en maskin läsnings bar tecken ström. Read API använder våra senaste modeller och arbetar med text på en rad olika ytor och bakgrunder, till exempel kvitton, affischer, visitkort, brev och whiteboardtavlor. För närvarande är engelska det enda språk som stöds.

Du kan också använda [OCR-API (optisk tecken läsning)](concept-recognizing-text.md#ocr-optical-character-recognition-api) för att extrahera utskriven text på flera språk. Om det behövs korrigerar OCR rotationen av den tolkade texten och anger ramkoordinater för varje ord. OCR har stöd för 25 olika språk och identifierar automatiskt språket för den tolkade texten.

## <a name="moderate-content-in-images"></a>Moderera innehåll i bilder

Du kan använda Visuellt innehåll för att [identifiera vuxna innehåll](concept-detecting-adult-content.md) i en bild och returnera konfidensts resultat för olika klassificeringar. Tröskelvärdet för flaggat innehåll kan ställas in på en rörlig skala för att passa dina inställningar.

## <a name="use-containers"></a>Använda containrar

[Använd containrar för visuellt innehåll](computer-vision-how-to-install-containers.md) för att känna igen tryckt och handskriven text lokalt genom att installera en standardiserad Docker-container som är närmare till dina data.

## <a name="image-requirements"></a>Avbildningskrav

Visuellt innehåll kan analysera bilder som uppfyller följande krav:

- Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Bildens dimensioner måste vara större än 50 x 50 bildpunkter
  - För läsnings-API: t måste bildens mått vara mellan 50 x 50 och 10000 x 10000 bild punkter.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Som med alla Cognitive Services bör utvecklare som använder tjänsten Visuellt innehåll känna till Microsofts policyer gällande kunddata. Läs mer på [Cognitive Services-sidan](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg

Kom igång med Visuellt innehåll genom att följa en snabbstartsguide:

- [Snabb start: Visuellt innehåll .NET SDK](quickstarts-sdk/csharp-sdk.md)
- [Snabb start: Visuellt innehåll python SDK](quickstarts-sdk/python-sdk.md)
- [Snabb start: Visuellt innehåll Java SDK](quickstarts-sdk/java-sdk.md)
