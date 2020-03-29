---
title: Tryckt, handskriven textigenkänning - Datorseende
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till att känna igen tryckt och handskriven text i bilder med hjälp av API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220164"
---
# <a name="recognize-printed-and-handwritten-text"></a>Känna igen tryckt och handskriven text

Computer Vision tillhandahåller ett antal tjänster som identifierar och extraherar tryckt eller handskriven text som visas i bilder. Detta är användbart i en mängd olika scenarier, till exempel anteckningar, journaler, säkerhet och banktjänster. I följande tre avsnitt beskrivs tre olika API:er för textigenkänning, som var och en är optimerad för olika användningsfall.

## <a name="read-api"></a>Läs API

Läs-API:et identifierar textinnehåll i en bild med hjälp av våra senaste igenkänningsmodeller och omvandlar den identifierade texten till en maskinläsbar teckenström. Den är optimerad för texttunga bilder (till exempel dokument som har skannats digitalt) och för bilder med mycket visuellt brus. Det avgör vilken igenkänningsmodell som ska användas för varje textrad och stöder bilder med både utskriven och handskriven text. Läs-API:et körs asynkront eftersom större dokument kan ta flera minuter att returnera ett resultat.

Read-åtgärden underhåller de ursprungliga radgrupperingarna för tolkade ord i utdata. Varje rad levereras med begränsningsramkoordinater, och varje ord inom raden har också sina egna koordinater. Om ett ord erkändes med lågt förtroende, förmedlas den informationen också. Mer information finns i [referensdokumenten](https://go.microsoft.com/fwlink/?linkid=2118322) läs [API-referensdokument](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) och läs API Preview.

> [!NOTE]
> Den här funktionen är endast tillgänglig för engelsk och spansk (förhandsgranskningstext).

### <a name="image-requirements"></a>Avbildningskrav

Läs-API:et fungerar med bilder som uppfyller följande krav:

- Bilden måste visas i JPEG-, PNG-, BMP-, PDF- eller TIFF-format.
- Bildens mått måste vara mellan 50 x 50 och 10000 x 1 0000 pixlar. PDF-sidor måste vara 17 x 17 tum eller mindre.
- Bildens filstorlek måste vara mindre än 20 MB.The file size of the image must be less than 20 megabytes (MB).

### <a name="limitations"></a>Begränsningar

Om du använder en prenumeration på den kostnadsfria nivån bearbetas bara de två första sidorna i ett PDF- eller TIFF-dokument. Med en betald prenumeration kommer den att bearbeta upp till 200 sidor. Observera också att API:et identifierar maximalt 300 rader per sida.

## <a name="ocr-optical-character-recognition-api"></a>OCR-API (optisk teckenigenkänning)

OCR-API (Optical Character Recognition) liknar Läs-API:et, men det körs synkront och är inte optimerat för stora dokument. Den använder en tidigare igenkänningsmodell men fungerar med fler språk. se [Språkstöd](language-support.md#text-recognition) för en fullständig lista över språk som stöds.

Om det behövs korrigerar OCR den tolkade textens rotationsförskjutning genom att returnera rotationsförskjutningen i grader runt den vågräta bildaxeln. OCR tillhandahåller också ramkoordinaterna för varje ord, vilket visas i följande bild.

![En bild som roteras och dess text läss och avgränsas](./Images/vision-overview-ocr.png)

Mer information finns i [OCR-referensdokumenten.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)

### <a name="image-requirements"></a>Avbildningskrav

OCR-API:et fungerar på avbildningar som uppfyller följande krav:

* Bilden måste visas i JPEG-, PNG-, GIF- eller BMP-format.
* Storleken på indatabilden måste vara mellan 50 x 50 och 4200 x 4200 pixlar.
* Texten i bilden kan roteras med en multipel av 90 grader plus en liten vinkel på upp till 40 grader.

### <a name="limitations"></a>Begränsningar

På fotografier där texten är dominerande kan falska positiva tecken komma från delvis erkända ord. På vissa fotografier, särskilt foton utan text, kan precisionen variera beroende på vilken typ av bild det rör sig om.

## <a name="recognize-text-api"></a>Identifiera text-API

> [!NOTE]
> Api:et för identifiera text är inaktuellt till förmån för Läs-API:et. Läs-API:et har liknande funktioner och uppdateras för att hantera PDF-, TIFF- och flersidiga filer.

API:et för identifiera text liknar OCR, men körs asynkront och använder uppdaterade igenkänningsmodeller. Mer information finns i [referensdokumenten för identifiera text-API:er.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)

### <a name="image-requirements"></a>Avbildningskrav

API:et för igen recognizetext fungerar med bilder som uppfyller följande krav:

- Bilden måste visas i JPEG-, PNG- eller BMP-format.
- Bildens mått måste vara mellan 50 x 50 och 4200 x 4200 pixlar.
- Bildens filstorlek måste vara mindre än 4 MB.The file size of the image must be less than 4 megabytes (MB).

## <a name="limitations"></a>Begränsningar

Exaktheten i textigenkänningsåtgärder beror på bildernas kvalitet. Följande faktorer kan orsaka en felaktig avläsning:

* Suddiga bilder.
* Handskriven eller kursiv text.
* Konstnärliga teckensnittsstilar.
* Liten teckenstorlek.
* Komplexa bakgrunder, skuggor eller motljus över text eller perspektivförvrängningar.
* Överdimensionerade eller saknade stora bokstäver i början av ord.
* Nedsänkt text, upphöjd eller genomstruken text.

## <a name="next-steps"></a>Nästa steg

Följ [snabbstarten Extrahera text (Läs)](./QuickStarts/CSharp-hand-text.md) för att implementera textigenkänning i en enkel C#-app.
