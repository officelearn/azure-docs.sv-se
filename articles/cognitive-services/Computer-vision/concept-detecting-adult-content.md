---
title: Beskriv vuxet eller olämpligt innehåll - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för identifiering av vuxet eller olämpligt innehåll i bilder med den APi för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7f343f22eb881cf58a024f8464ffd0d615fe06e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158901"
---
# <a name="detecting-adult-and-racy-content"></a>Identifiera vuxet eller olämpligt innehåll

Bland de olika visuella kategorierna är gruppen med innehåll som är olämpligt för barn som känner igen sådant material och begränsar bilder med sexuellt innehåll. Filtret för stötande och olämpligt innehåll kan ställas in på en skala för att passa dina preferenser.

## <a name="defining-adult-and-racy-content"></a>Definiera vuxet eller olämpligt innehåll

Bland de olika visuella funktioner som omfattas av den [analysera bild metoden](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), vuxet visual funktionen gör det möjligt för identifiering av bilder med vuxet och vågat innehåll. ”Vuxet” avbildningar definieras som sådana som är pornografiskt sin natur och ofta föreställer nakenhet och sexuellt åtgärder. ”Vågat” bilder har definierats som bilder som sexuellt tyder sin natur och innehåller ofta mindre sexuellt explicit innehåll än avbildningar som taggats som ”vuxen”. Vuxet visual funktionstyp används ofta för att begränsa visning av bilder som innehåller sexuellt tyder och sexuellt explicit innehåll.

## <a name="identifying-adult-and-racy-content"></a>Identifiera vuxet eller olämpligt innehåll

Analysera bild-metoden returnerar två egenskaper `isAdultContent` och `isRacyContent`, JSON-svar med metoden, ange vuxet eller olämpligt innehåll. Båda egenskaperna returnerar ett booleskt värde, true eller false. Metoden returnerar också två egenskaper `adultScore` och `racyScore`, som representerar, respektive förtroende poängen för att identifiera vuxet eller olämpligt innehåll. Ett förtroende-filter för vuxet och vågat innehåll kan ställas in på en glidande skala för att hantera dina inställningar baserat på din situation.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [identifierar domänspecifika innehåll](concept-detecting-domain-content.md) och [identifierar ansikten](concept-detecting-faces.md).
