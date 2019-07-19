---
title: Identifiera skriven/handskriven text, Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör igenkänning av utskrift och handskriven text i bilder med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cfbbd0b353699c4b04ede07df0450e66bd59612f
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311953"
---
# <a name="recognize-printed-and-handwritten-text"></a>Känna igen tryckt och handskriven text

Visuellt innehåll tillhandahåller ett antal tjänster som identifierar och extraherar tryckt text eller handskriven text som visas i bilder. Detta är användbart i många olika scenarier, till exempel anmärkningar, medicinska poster, säkerhet och bank tjänster. I följande tre avsnitt beskrivs tre olika API: er för text igenkänning, som är optimerade för olika användnings fall.

## <a name="read-api"></a>Läs-API

Read API identifierar text innehåll i en avbildning med hjälp av våra senaste igenkännings modeller och konverterar den identifierade texten till en maskin läsnings bar tecken ström. Den är optimerad för text intensiva avbildningar (till exempel dokument som har skannats digitalt) och för bilder med stor visuell brus. Den avgör vilken igenkännings modell som ska användas för varje textrad, stöd för bilder med både utskrift och handskriven text. Read API körs asynkront eftersom det kan ta flera minuter för större dokument att returnera ett resultat.

Läs åtgärden underhåller de ursprungliga rad grupperna av identifierade ord i dess utdata. Varje rad kommer med koordinater för markerings rutor, och varje ord på raden har också sina egna koordinater. Om ett ord identifierades med låg exakthet förmedlas även den informationen. Mer information finns i [referens dokumenten för Read API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) .

> [!NOTE]
> Den här funktionen är endast tillgänglig för engelsk text.

### <a name="image-requirements"></a>Avbildningskrav

Read API fungerar med avbildningar som uppfyller följande krav:

- Bilden måste visas i JPEG-, PNG-, BMP-, PDF-eller TIFF-format.
- Bildens mått måste vara mellan 50 x 50 och 10000 x 10000 bild punkter. PDF-sidor måste vara 17 × 17 tum eller mindre.
- Bildens fil storlek måste vara mindre än 20 megabyte (MB).

### <a name="limitations"></a>Begränsningar

Om du använder en prenumeration på kostnads fri nivå bearbetar Read API endast de första två sidorna i ett PDF-eller TIFF-dokument. Med en betald prenumeration kommer den att bearbeta upp till 200 sidor. Observera också att API: et ska identifiera högst 300 rader per sida.

## <a name="ocr-optical-character-recognition-api"></a>OCR-API (optisk tecken läsning)

Visuellt innehåll ' OCR-API: n (optisk tecken läsning) liknar Read API, men körs synkront och är inte optimerad för stora dokument. Den använder en tidigare igenkännings modell men fungerar med fler språk. Se [språk stöd](language-support.md#text-recognition) för en fullständig lista över de språk som stöds.

Vid behov korrigerar OCR rotationen av den tolkade texten genom att returnera rotations förskjutningen i grader om den vågräta bild axeln. OCR innehåller också koordinaterna för varje ord, som du ser i följande bild.

![En bild som roteras och dess text läses och avgränsas](./Images/vision-overview-ocr.png)

Mer information finns i [referens dokument för OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) .

### <a name="image-requirements"></a>Avbildningskrav

OCR-API: et fungerar på avbildningar som uppfyller följande krav:

* Bilden måste visas i formatet JPEG, PNG, GIF eller BMP.
* Storleken på indata-bilden måste vara mellan 50 x 50 och 4200 x 4200 bild punkter.
* Texten i bilden kan roteras med en multipel på 90 grader plus en liten vinkel på upp till 40 grader.

### <a name="limitations"></a>Begränsningar

I fotografier där texten är dominerande kan falska positiva identifieringar komma från delvis identifierade ord. I vissa fotografier, särskilt foton utan text, kan precisionen variera beroende på typen av bild.

## <a name="recognize-text-api"></a>Identifiera text-API

> [!NOTE]
> Identifiera text-API: et är inaktuellt med Läs-API: et. Read API har liknande funktioner och uppdateras för att hantera PDF-, TIFF-och flersidiga filer.

Identifiera text API liknar OCR, men körs asynkront och använder uppdaterade igenkännings modeller. Mer information finns i [referens dokumenten för identifiera text API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) .

### <a name="image-requirements"></a>Avbildningskrav

Identifiera text-API: et fungerar med avbildningar som uppfyller följande krav:

- Bilden måste visas i formatet JPEG, PNG eller BMP.
- Bildens mått måste vara mellan 50 x 50 och 4200 x 4200 bild punkter.
- Bildens fil storlek måste vara mindre än 4 MB.

## <a name="limitations"></a>Begränsningar

Precisionen för text igenkännings åtgärder beror på bildens kvalitet. Följande faktorer kan orsaka en felaktig läsning:

* Suddiga bilder.
* Handskriven eller kursiv text.
* Konstnärliga teckensnittsstilar.
* Liten teckenstorlek.
* Komplexa bakgrunder, skuggor eller motljus över text eller perspektivförvrängningar.
* Stora bokstäver eller saknade versaler i början av ord.
* Nedsänkt text, upphöjd eller genomstruken text.

## <a name="next-steps"></a>Nästa steg

Följ snabb starten för att [extrahera tryckt text (OCR)](./quickstarts/csharp-print-text.md) för att implementera text igenkänning C# i en enkel app.
