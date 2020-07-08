---
title: Optisk tecken läsning (OCR) – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör optisk tecken läsning (OCR) från bilder och dokument med tryckt och handskriven text med hjälp av API för visuellt innehåll.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: d1c642a660b24cfc54c9c4308b8956582e13d50a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954748"
---
# <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Visuellt innehåll innehåller en ny djup inlärnings funktion för OCR (optisk tecken läsning) som extraherar tryckt eller handskriven text från bilder och PDF-dokument. Visuellt innehåll extraherar text från både analoga dokument (bilder, skannade dokument) och avbildade dokument. Du kan extrahera text från bilder i jokertecken, till exempel foton av licens plattor eller behållare med serie nummer, samt från dokument fakturor, fakturor, ekonomiska rapporter, artiklar med mera. Den här OCR-funktionen är tillgänglig som en del av den hanterade tjänsten i molnet eller lokalt (behållare). Det stöder också virtuella nätverk och privata slut punkter för att uppfylla behoven i företags klassens efterlevnad och sekretess.

## <a name="read-api"></a>Läs-API 

Visuellt innehåll [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) är Microsofts senaste OCR-teknik som extraherar tryckt text (sju språk), handskriven text (endast engelska), siffror och valuta symboler från bilder och PDF-dokument med flera sidor. Det är optimerat för att extrahera text från en text – tunga bilder och PDF-dokument med flera sidor med blandade språk. Det stöder identifiering av skriven och handskriven text (endast engelska) i samma bild eller dokument. Du hittar en fullständig lista över språk som stöds på sidan [språk stöd för visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) .

### <a name="how-it-works"></a>Så här fungerar det

[Läsnings-API: et](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) stöder text intensiva dokument på upp till 2000 sidor och körs därför asynkront. Det första steget är att anropa Läs åtgärden. Läs åtgärden tar en bild eller ett PDF-dokument som indata och returnerar ett åtgärds-ID. 

Det andra steget är att anropa åtgärden [Hämta resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Den här åtgärden tar i det åtgärds-ID som skapades av Läs åtgärden. Den returnerar sedan det extraherade text innehållet från din avbildning eller ditt dokument i form av JSON. JSON-svaret underhåller de ursprungliga rad grupperna av identifierade ord. Den innehåller de extraherade text raderna och deras avgränsnings Rute koordinater. Varje textrad innehåller alla extraherade ord med deras koordinater och en förtroende poäng.

Vid behov kan du läsa korrigera rotationen för den identifierade sidan genom att returnera rotations förskjutningen i grader om den vågräta bild axeln, som visas i följande bild.

![En bild som roteras och dess text läses och avgränsas](./Images/vision-overview-ocr-read.png)

Följ snabb starten för [extrahering av skrivna och handskrivna text](./QuickStarts/CSharp-hand-text.md) för att implementera OCR med C# och REST API.

### <a name="input-requirements"></a>Krav för indatamängd

Läs-API: et tar följande indata:
* Fil format som stöds: JPEG, PNG, BMP, PDF och TIFF
* För PDF och TIFF bearbetas upp till 2000 sidor. För prenumeranter på den kostnads fria nivån bearbetas bara de första två sidorna.
* Fil storleken måste vara mindre än 50 MB och dimensioner minst 50 x 50 bild punkter och högst 10000 x 10000 bild punkter.
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.


### <a name="text-from-images"></a>Text från bilder

Följande Read API-utdata visar de extraherade text raderna och orden från en bild med text i olika vinklar, färger och teckensnitt

![En bild som roteras och dess text läses och avgränsas](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Text från dokument

Förutom bilder tar Read-API: t ett PDF-dokument som inmatade.

![En bild som roteras och dess text läses och avgränsas](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Handskriven text på engelska

Just nu stöder Läs åtgärden extrahering av handskriven text exklusivt på engelska.

![En bild som roteras och dess text läses och avgränsas](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Utskriven text i språk som stöds

Read API stöder extrahering av utskriven text på engelska, spanska, tyska, franska, italienska, portugisiska och nederländska språk. Om ditt scenario kräver stöd för fler språk, se Översikt över OCR API i det här dokumentet. Se listan över alla språk som [stöds](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)

![En bild som roteras och dess text läses och avgränsas](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Stöd för blandade språk

Read API stöder bilder och dokument med flera språk, vanligt vis kallade blandade språk dokument. Det gör det genom att klassificera varje text rad i dokumentet på det identifierade språket innan du extraherar text innehållet.

![En bild som roteras och dess text läses och avgränsas](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla kognitiva tjänster bör utvecklare som använder Läs tjänsten vara medvetna om Microsofts principer för kund information. Mer information finns på sidan Cognitive Services på [Microsoft Trust Center](https://www.microsoft.com/en-us/trust-center/product-overview) .

### <a name="deploy-on-premises"></a>Distribuera lokalt

Läs är även tillgänglig som en Docker-behållare (för hands version) så att du kan distribuera nya OCR-funktioner i din egen miljö. Behållare är fantastiska för särskilda säkerhets-och data styrnings krav. Se [Installera och köra Läs behållare.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR-API

[OCR-API: t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) använder en äldre igenkännings modell, stöder bara bilder och körs synkront, och returneras omedelbart med den identifierade texten. Det stöder [fler språk](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) än Read API.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [läsa 3,0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Följ snabb starten för att [Extrahera text](./QuickStarts/CSharp-hand-text.md) för att implementera OCR med C#, Java, java script eller python tillsammans med REST API.
