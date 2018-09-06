---
title: Översikt över Custom Vision Service-maskininlärning – Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service är en Microsoft Cognitive-tjänst där du kan skapa anpassade bildklassificerare på Azure-plattformen.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: d2daf7c211f9474f5636b6af69c5b700d597aa14
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285252"
---
# <a name="what-is-the-custom-vision-service"></a>Vad är Custom Vision Service?

Custom Vision Service är en Microsoft Cognitive-tjänst där du kan skapa anpassade bildklassificerare. Det gör det enkelt och snabbt att bygga, distribuera och förbättra en bildklassificerare. Custom Vision Service tillhandahåller ett REST-API och ett webbgränssnitt för att ladda upp dina bilder och träna klassificeraren.

## <a name="what-does-custom-vision-service-do-well"></a>Hur fungerar Custom Vision Service som bäst?

Custom Vision Service fungerar bäst om ett objekt som du försöker klassificera är framträdande i bilden. 

Få bilder krävs för att skapa en klassificerare eller detektor. 50 bilder per klass räcker för att påbörja din prototyp. Metoderna som Custom Vision Service använder är robusta för skillnader, vilket betyder att du kan börja skapa prototyper med så lite data. Det innebär att Custom Vision Service inte passar så bra för scenarier där du vill identifiera små skillnader. Till exempel små fel i kvalitetskontroller.

## <a name="next-steps"></a>Nästa steg

[Lär dig att skapa en klassificerare](getting-started-build-a-classifier.md)
