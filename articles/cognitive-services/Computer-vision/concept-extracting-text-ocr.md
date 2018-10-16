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
ms.openlocfilehash: 52b6265722d5cfbf8baf54e1785ace627aa0892f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341675"
---
# <a name="extracting-text-with-ocr"></a>Extrahera text med OCR

Optisk teckenläsning (OCR)-teknik i visuellt identifierar textinnehåll i en bild och extraherar identifierade texten till en maskinläsningsbar teckenström. Du kan använda resultatet för sökning och flera andra ändamål som medicinska journaler, säkerhet och banktjänster. Den identifierar språket automatiskt. OCR sparar tid och underlättar för användarna genom att de kan ta foton av text istället för att skriva texten.

OCR stöder 25 olika språk. Dessa språk är: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, Serbiska (kyrillisk och latinsk) Slovakiska, spanska, svenska och turkiska.

Om det behövs, korrigerar OCR rotationen av den tolkade texten i grader kring vågrät bild-axeln. OCR ger ramens koordinaterna i varje ord som visas i följande bild.

![OCR-översikt](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>OCR-krav

Visuellt innehåll extraherar text med OCR från avbildningar som uppfyller följande krav:

* Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
* Storleken på den inkommande avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter


Inmatad bild kan roteras dubbla av 90 grader plus en liten vinkeln för upp till 40 grader.

## <a name="improving-ocr-accuracy"></a>Förbättra OCR-precision

Hur korrekt referensdatorns textigenkänning beror på kvaliteten på avbildningen. En felaktig läsning kan ha orsakats av följande situationer:

* Suddiga bilder.
* Handskriven eller kursiv text.
* Konstnärlig teckensnittsstilar.
* Liten textstorlek.
* Komplexa bakgrunder, skuggor eller antireflex via text eller ett perspektiv förvrängningar.
* Stora eller saknas versaler i början av ord
* Nedsänkt text, upphöjd eller genomstruken text.

### <a name="ocr-limitations"></a>OCR-begränsningar

Falska positiva identifieringar kan komma från delvis identifierade ord på fotografier där texten är dominerande. På vissa fotografier, särskilt foton någon text varierar precision mycket beroende på vilken typ av avbildning.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [känna igen utskrivna och handskriven text](concept-recognizing-text.md).
