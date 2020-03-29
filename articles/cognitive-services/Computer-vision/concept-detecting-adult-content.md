---
title: Vuxen, racy, blodiga innehåll - Datorseende
titleSuffix: Azure Cognitive Services
description: Begrepp som är relaterade till att upptäcka barnförbjudet innehåll i bilder med hjälp av APi för datorseende.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718516"
---
# <a name="detect-adult-content"></a>Identifiera barnförbjudet innehåll

Computer Vision kan upptäcka vuxet material i bilder så att utvecklare kan begränsa visningen av dessa bilder i sin programvara. Innehållsflaggor används med en poäng mellan noll och en så att utvecklare kan tolka resultaten enligt sina egna preferenser.

> [!NOTE]
> Mycket av den här funktionen erbjuds av Azure [Content Moderator-tjänsten.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Se det här alternativet för lösningar på mer rigorösa scenarier för innehållsmoderering, till exempel textmoderering och arbetsflöden för mänsklig granskning.

## <a name="content-flag-definitions"></a>Definitioner av innehållsflagga

Inom klassificeringen "vuxen" finns flera olika kategorier:

- **Vuxna** bilder definieras som de som uttryckligen är sexuella till sin natur och ofta skildrar nakenhet och sexuella handlingar.
- **Racy** bilder definieras som bilder som är sexuellt suggestiva i naturen och ofta innehåller mindre sexuellt explicit innehåll än bilder taggade som **vuxen**.
- **Blodiga** bilder definieras som de som skildrar gore.

## <a name="use-the-api"></a>Använda API:et

Du kan identifiera barnförbjudet innehåll med [API:et analyseravbildning.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) När du lägger `Adult` till värdet för frågeparametern **visualFeatures returnerar** API:et tre booleska egenskaper&mdash;`isAdultContent`och `isRacyContent` `isGoryContent` &mdash;i dess JSON-svar. Metoden returnerar också&mdash;`adultScore` `racyScore`motsvarande `goreScore` &mdash;egenskaper och som representerar konfidenspoäng mellan noll och en för varje kategori.

- [Snabbstart: Analysera en bild (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)
