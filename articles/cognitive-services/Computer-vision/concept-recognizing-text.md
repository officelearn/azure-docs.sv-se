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
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313185"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Känna igen tryckt och handskriven text

Visuellt innehåll kan identifiera och extrahera utskrivna eller handskriven text från bilder för olika objekt med olika ytor och bakgrunder, till exempel kvitton, affischer, visitkort, bokstäver och whiteboardtavlor.

Text för funktionen är mycket lik [optisk teckenläsning (OCR)](concept-extracting-text-ocr.md), men till skillnad från OCR den asynkront och använder uppdateras modeller för taligenkänning.

> [!NOTE]
> Den här tekniken är för närvarande en förhandsversion och är endast tillgänglig för engelsk text.

## <a name="text-recognition-requirements"></a>Krav för text

Visuellt innehåll kan känna igen utskrivna och handskriven text i bilder som uppfyller följande krav:

- Bilden måste vara visas i JPEG, PNG eller BMP-format
- Filstorleken måste vara mindre än 4 megabyte (MB)
- Storleken för avbildningen måste vara mellan 50 x 50 och 4200 x 4200 bildpunkter

## <a name="next-steps"></a>Nästa steg

Se den [identifiera text referensdokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) vill veta mer.