---
title: 'Känner igen utskrivna, handskriven text: visuellt innehåll'
titleSuffix: Azure Cognitive Services
description: Begrepp att känna igen utskrivna och handskriven text i bilder med den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9bb574fcb9782aad41ea0fd276b8addee19caf01
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588983"
---
# <a name="recognize-printed-and-handwritten-text"></a>Känna igen tryckt och handskriven text

Visuellt innehåll ger ett antal tjänster som kan identifiera och extrahera utskrivna eller handskriven text som visas i bilder. Detta är användbart i en mängd olika scenarier, till exempel notetaking, medicinska journaler, säkerhet och banktjänster. I följande tre avsnitt detalj tre olika textigenkänning API: er, som var och en optimerad för olika användningsfall.

## <a name="read-api"></a>Få tillgång till API

Läs API: et identifierar textinnehåll i en avbildning med hjälp av våra senaste modeller för taligenkänning och konverterar den identifierade texten till en maskinläsningsbar teckenström. Tjänsten är optimerad för textintensiv avbildningar (t.ex dokument som har genomsökts digitalt) och avbildningar med en massa visual bruset. Den kan köras asynkront eftersom större dokument kan ta flera minuter att beräkna ett resultat.

Läs-åtgärden bevarar de ursprungliga rad grupperingarna av identifierade ord i dess utdata. Varje rad med avgränsar koordinater för avgränsningsfält, och varje ord i raden har också sin egen koordinater. Om ett ord identifieras med låg tryggt som informationen överförs även. Se den [Läs API-referensdokumenten](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) vill veta mer.

> [!NOTE]
> Den här funktionen är för närvarande en förhandsversion och är endast tillgänglig för engelsk text.

### <a name="image-requirements"></a>Avbildningskrav

Läs API fungerar med avbildningar som uppfyller följande krav:

- Bilden måste vara visas i JPEG, PNG, BMP, PDF eller TIFF-format.
- Storleken för avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter. PDF-sidor måste vara 17 x 17 tum eller mindre.
- Filstorleken för avbildningen måste vara mindre än 20 megabyte (MB).

### <a name="limitations"></a>Begränsningar

Om du använder en prenumeration på kostnadsfri nivå, bearbetar Läs API endast de första två sidorna i en PDF- eller TIFF-dokumentet. Med en betald prenumeration bearbetas upp till 200 sidor. Observera också att API: et identifierar högst 300 rader per sida.

## <a name="ocr-optical-character-recognition-api"></a>OCR (optisk teckenläsning) API

API för visuellt innehåll optisk teckenläsning (OCR) liknar Läs-API, men det körs synkront och inte har optimerats för stora dokument. Det använder en tidigare igenkänningsfunktion för men fungerar med fler språk.

OCR stöder 25 olika språk: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, serbiska (kyrillisk och latinsk) slovakiska, spanska, svenska och turkiska. OCR identifierar automatiskt språket för den identifierade texten.

Om det behövs, korrigerar OCR rotationen av den tolkade texten genom att returnera rotational förskjutningen i grader om axeln vågrät bild. OCR innehåller också ramens koordinaterna i varje ord som visas i följande bild.

![Ett diagram som illustrerar en bild roteras och texten som Läs- och avgränsad rader](./Images/vision-overview-ocr.png)

Se den [OCR referensdokument](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) vill veta mer.

### <a name="image-requirements"></a>Avbildningskrav

OCR-API fungerar på avbildningar som uppfyller följande krav:

* Bilden måste vara visas i JPEG, PNG-, GIF- eller BMP-format.
* Storleken på den inkommande avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter.
* Texten i bilden kan roteras dubbla av 90 grader plus en liten vinkeln för upp till 40 grader.

### <a name="limitations"></a>Begränsningar

Falska positiva identifieringar kan komma från delvis identifierade ord på fotografier där texten är dominerande. Precisionen kan variera beroende på vilken typ av avbildning på vissa fotografier, särskilt foton någon text.

## <a name="recognize-text-api"></a>Identifiera Text API

> [!NOTE]
> Identifiera Text-API har ersatts av Läs-API. Läs API: et har liknande funktioner och uppdateras för att hantera PDF, TIFF och filer med flera sidor.

Identifiera Text-API är liknande OCR, men den asynkront och använder uppdaterade igenkänning av modeller. Se den [identifiera Text-API-referensdokumenten](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) vill veta mer.

### <a name="image-requirements"></a>Avbildningskrav

Identifiera Text-API fungerar med avbildningar som uppfyller följande krav:

- Bilden måste vara visas i JPEG, PNG eller BMP-format.
- Storleken för avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter.
- Filstorleken för avbildningen måste vara mindre än 4 MB (megabyte).

## <a name="improve-results"></a>Förbättra resultaten

Hur korrekt referensdatorns text igenkänning av operations beror på kvaliteten på bilderna. Följande faktorer kan orsaka en felaktig läsning:

* Suddiga bilder.
* Handskriven eller kursiv text.
* Konstnärliga teckensnittsstilar.
* Liten teckenstorlek.
* Komplexa bakgrunder, skuggor eller motljus över text eller perspektivförvrängningar.
* Stora eller saknas versaler i början av ord.
* Nedsänkt text, upphöjd eller genomstruken text.

## <a name="next-steps"></a>Nästa steg

Följ den [extrahera utskrivna text (OCR)](./quickstarts/csharp-print-text.md) Snabbstart för att implementera textigenkänning i en enkel C# app.
