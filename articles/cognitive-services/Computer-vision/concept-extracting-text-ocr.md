---
title: Extrahera text med optisk teckenläsning (OCR) - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för att extrahera text med optisk teckenläsning (OCR) med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310448"
---
# <a name="extract-text-with-optical-character-recognition"></a>Extrahera text med optisk teckenläsning

Funktionen för datorn Vision optisk teckenläsning (OCR) identifierar textinnehåll i en bild och konverterar den identifierade texten till en maskinläsningsbar teckenström. Du kan använda resultatet för många exempelvis sökning, medicinska journaler, säkerhet och banktjänster. 

OCR stöder 25 olika språk: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, serbiska (kyrillisk och latinsk) slovakiska, spanska, svenska och turkiska. OCR identifierar automatiskt språket för den identifierade texten.

Om det behövs, korrigerar OCR rotationen av den tolkade texten genom att returnera rotational förskjutningen i grader om axeln vågrät bild. OCR innehåller också ramens koordinaterna i varje ord som visas i följande bild.

![Ett diagram som illustrerar en bild roteras och texten som Läs- och avgränsad rader](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>Avbildningskrav

Visuellt innehåll extraherar text med OCR från avbildningar som uppfyller följande krav:

* Bilden måste vara i JPEG-, PNG-, GIF- eller BMP-format
* Storleken på den inkommande avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter
* Texten i bilden kan roteras dubbla av 90 grader plus en liten vinkeln för upp till 40 grader.

## <a name="improving-ocr-accuracy"></a>Förbättra OCR-precision

Hur korrekt referensdatorns textigenkänning är beror på bildkvaliteten. En felaktig läsning kan ha orsakats av följande:

* Suddiga bilder.
* Handskriven eller kursiv text.
* Konstnärliga teckensnittsstilar.
* Liten teckenstorlek.
* Komplexa bakgrunder, skuggor eller motljus över text eller perspektivförvrängningar.
* För stora eller saknade versaler i början på ord
* Nedsänkt text, upphöjd eller genomstruken text.

### <a name="ocr-limitations"></a>OCR-begränsningar

Falska positiva identifieringar kan komma från delvis identifierade ord på avbildningar där texten är dominanta. På vissa bilder särskilt foton någon text varierar precision mycket beroende på vilken typ av avbildning.

## <a name="next-steps"></a>Nästa steg

Se den [OCR referensdokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) vill veta mer.
