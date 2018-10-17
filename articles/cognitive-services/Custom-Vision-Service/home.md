---
title: Vad är Custom Vision Service?
titlesuffix: Azure Cognitive Services
description: Custom Vision Service låter dig skapa anpassade bildklassificerare i Azure-molnet.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 1d3d1a82cf59b06625487fb241a63f51352e18e5
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365867"
---
# <a name="what-is-the-custom-vision-service"></a>Vad är Custom Vision Service?

Custom Vision Service är en Azure Cognitive-tjänst som låter dig skapa anpassade bildklassificerare. Det gör det enkelt och snabbt att bygga, distribuera och förbättra en bildklassificerare. Custom Vision Service tillhandahåller ett REST-API och ett webbgränssnitt för att ladda upp dina bilder och träna klassificeraren.

## <a name="what-does-custom-vision-service-do-well"></a>Hur fungerar Custom Vision Service som bäst?

Custom Vision Service fungerar bäst om ett objekt som du försöker klassificera är framträdande i bilden. 

Få bilder krävs för att skapa en klassificerare eller detektor. 50 bilder per klass räcker för att påbörja din prototyp. Metoderna som Custom Vision Service använder är robusta för skillnader, vilket betyder att du kan börja skapa prototyper med så lite data. Det innebär att Custom Vision Service inte passar så bra för scenarier där du vill identifiera små skillnader. Till exempel små fel i kvalitetskontroller.

## <a name="next-steps"></a>Nästa steg

[Lär dig att skapa en klassificerare](getting-started-build-a-classifier.md)
