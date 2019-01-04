---
title: Extrahera text med OCR - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för att extrahera text med optisk teckenläsning (OCR) med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 07d3f5d365fa5c552ccb61c97532a9931b7e282e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583688"
---
# <a name="extracting-text-with-optical-character-recognition"></a>Extrahera text med optisk teckenläsning

Optisk teckenläsning (OCR)-teknik i visuellt identifierar textinnehåll i en bild och extraherar identifierade texten till en maskinläsningsbar teckenström. Du kan använda resultatet för sökning och flera andra ändamål som medicinska journaler, säkerhet och banktjänster. Den identifierar språket automatiskt. OCR sparar tid och underlättar för användarna genom att göra det möjligt för dem att helt enkelt ta ett kort istället för att skriva ner informationen för hand.

OCR stöder 25 olika språk. Dessa språk är: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, serbiska (kyrillisk och latinsk) slovakiska, spanska, svenska och turkiska.

Om det behövs korrigerar OCR rotationen av den tolkade texten i grader kring den vågräta bildaxeln. OCR ger ramens koordinaterna i varje ord som visas i följande bild.

![Ett diagram som illustrerar en bild roteras och texten som Läs- och avgränsad rader](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>OCR-krav

Visuellt innehåll extraherar text med OCR från avbildningar som uppfyller följande krav:

* Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
* Storleken på den inkommande avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter


Inmatad bild kan roteras dubbla av 90 grader plus en liten vinkeln för upp till 40 grader.

## <a name="improving-ocr-accuracy"></a>Förbättra OCR-precision

Hur korrekt referensdatorns textigenkänning är beror på bildkvaliteten. En felaktig inläsning kan ha orsakats av följande situationer:

* Suddiga bilder.
* Handskriven eller kursiv text.
* Konstnärliga teckensnittsstilar.
* Liten teckenstorlek.
* Komplexa bakgrunder, skuggor eller motljus över text eller perspektivförvrängningar.
* För stora eller saknade versaler i början på ord
* Nedsänkt text, upphöjd eller genomstruken text.

### <a name="ocr-limitations"></a>OCR-begränsningar

Falska positiva identifieringar kan komma från delvis identifierade ord på fotografier där texten är dominerande. På vissa fotografier, särskilt foton någon text varierar precision mycket beroende på vilken typ av avbildning.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [känna igen utskrivna och handskriven text](concept-recognizing-text.md).
