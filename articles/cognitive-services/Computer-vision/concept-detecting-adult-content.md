---
title: Beskriv vuxet eller olämpligt innehåll - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för identifiering av vuxet eller olämpligt innehåll i bilder med den APi för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368362"
---
# <a name="detect-adult-and-racy-content"></a>Identifiera vuxet eller olämpligt innehåll

Visuellt innehåll kan identifiera material som är olämpligt för barn i bilder så att utvecklare kan begränsa visning av sådana bilder i sina program. Innehåll flaggor tillämpas med en poäng mellan noll och ett så att utvecklare kan tolka resultaten efter egna önskemål. 

> [!NOTE]
> Den här funktionen är även erbjuds av den [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) service. Se detta alternativ för lösningar i strängare innehållsmoderering scenarier, till exempel textmoderering och arbetsflöden för mänsklig granskning.

## <a name="content-flag-definitions"></a>Innehåll flaggan definitioner

**Vuxet** avbildningar definieras som sådana som är pornografiskt sin natur och ofta föreställer nakenhet och sexuellt åtgärder. 

**Vågat** avbildningar är definierade som avbildningar som är sexuellt tyder sin natur och ofta innehåller mindre sexuellt explicit innehåll än avbildningar som **vuxet**. 

## <a name="identify-adult-and-racy-content"></a>Identifiera vuxet eller olämpligt innehåll

Den [analysera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API.

Analysera bild-metoden returnerar två booleska egenskaper `isAdultContent` och `isRacyContent`, JSON-svar med metoden för att ange vuxet eller olämpligt innehåll respektive. Metoden returnerar också två egenskaper `adultScore` och `racyScore`, som representerar förtroende poängen för att identifiera vuxet eller olämpligt innehåll respektive.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [identifierar domänspecifika innehåll](concept-detecting-domain-content.md) och [identifierar ansikten](concept-detecting-faces.md).
