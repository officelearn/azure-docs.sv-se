---
title: Extrahera text med OCR
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Begrepp för att extrahera text med optisk teckenläsning med visuellt i Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 07efe49c53b13094f3f67db3b3becb57a7c473d8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725544"
---
# <a name="extracting-text-with-ocr"></a>Extrahera text med OCR

Optisk teckenläsning (OCR)-teknik i visuellt identifierar textinnehåll i en bild och extraherar identifierade texten till en maskinläsningsbar teckenström. Du kan använda resultatet för sökning och flera andra ändamål som medicinska journaler, säkerhet och banktjänster. Den identifierar språket automatiskt. OCR sparar tid och underlättar för användarna genom att de kan ta foton av text istället för att skriva texten.

OCR stöder 25 olika språk. Dessa språk är: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, Serbiska (kyrillisk och latinsk) Slovakiska, spanska, svenska och turkiska.

Om det behövs, korrigerar OCR rotationen av den tolkade texten i grader kring vågrät bild-axeln. OCR ger ramens koordinaterna i varje ord som visas i följande bild.

![OCR-översikt](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>OCR-krav

Visuellt innehåll extraherar text med OCR från avbildningar som uppfyller följande krav:

* Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
* Storleken på den inkommande avbildningen måste vara mellan 40 x 40 och 3200 x 3200 bildpunkter
* Bilden får inte vara större än 10 megapixlar

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