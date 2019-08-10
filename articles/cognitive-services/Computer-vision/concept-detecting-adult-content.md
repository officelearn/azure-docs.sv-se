---
title: Innehåll för vuxna och vågat-Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifiering av vuxen och vågat innehåll i avbildningar med hjälp av Visuellt innehåll APi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ca5f35ab47822d74de556671c38886942d23d9ff
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946287"
---
# <a name="detect-adult-and-racy-content"></a>Identifiera vuxna och vågat innehåll

Visuellt innehåll kan identifiera vuxna material i bilder så att utvecklare kan begränsa visningen av sådana bilder i program varan. Innehålls flaggor används med ett resultat mellan noll och en så att utvecklarna kan tolka resultaten enligt sina egna inställningar. 

> [!NOTE]
> Den här funktionen erbjuds också av [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) -tjänsten. Se det här alternativet för lösningar till fler rigorösa innehålls redigerings scenarier, till exempel text moderator och mänsklig gransknings arbets flöden.

## <a name="content-flag-definitions"></a>Definition av innehålls flagga

**Vuxna** bilder definieras som de som är pornografiskt och som ofta visar nakenhet och sexuella handlingar. 

**Vågat** -avbildningar definieras som bilder som är sexuellt och ofta innehåller mindre sexuellt innehåll än bilder som taggats som **vuxen**. 

## <a name="identify-adult-and-racy-content"></a>Identifiera vuxen och vågat innehåll

[Analys](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) -API: et.

Metoden analysera avbildning returnerar två booleska egenskaper `isAdultContent` och `isRacyContent`, i JSON-svaret på metoden för att ange vuxen och vågat-innehåll. Metoden returnerar också två egenskaper `adultScore` och `racyScore`, som representerar förtroende poängen för att identifiera vuxna och vågat-innehåll.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp för att [identifiera domänanslutet innehåll](concept-detecting-domain-content.md) och [upptäcka ansikten](concept-detecting-faces.md).
