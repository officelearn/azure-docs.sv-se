---
title: Vuxen, vågat, fullständig-innehåll – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifiering av olämpligt innehåll i avbildningar med hjälp av Visuellt innehåll APi.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "71718516"
---
# <a name="detect-adult-content"></a>Identifiera vuxen innehåll

Visuellt innehåll kan identifiera vuxna material i bilder så att utvecklare kan begränsa visningen av dessa bilder i program varan. Innehålls flaggor används med ett resultat mellan noll och en så att utvecklarna kan tolka resultaten enligt sina egna inställningar.

> [!NOTE]
> Mycket av den här funktionen erbjuds av [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) -tjänsten. Se det här alternativet för lösningar till fler rigorösa innehålls redigerings scenarier, till exempel text moderator och mänsklig gransknings arbets flöden.

## <a name="content-flag-definitions"></a>Definition av innehålls flagga

Inom klassificeringen "vuxen" är flera olika kategorier:

- **Sexuella** bilder definieras som sådana som är explicit sexuella och som ofta visar nakenhet och sexuella handlingar.
- **Vågat** -avbildningar definieras som bilder som är sexuellt och ofta innehåller mindre sexuellt innehåll än bilder som taggats som **vuxen**.
- **Fullständig** -avbildningar definieras som de som visar Gore.

## <a name="use-the-api"></a>Använda API:et

Du kan identifiera innehåll för vuxna med API för att [analysera avbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . När du lägger till värdet för `Adult` i **visualFeatures** -Frågeparametern returnerar API tre booleska egenskaper &mdash; `isAdultContent` , `isRacyContent` och `isGoryContent` &mdash; i dess JSON-svar. Metoden returnerar också motsvarande egenskaper &mdash; `adultScore` , `racyScore` och `goreScore` &mdash; som representerar förtroende poängen mellan noll och en för varje respektive kategori.

- [Snabb start: analysera en avbildning (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Snabb start: analysera en avbildning (REST API)](./quickstarts/csharp-analyze.md)
