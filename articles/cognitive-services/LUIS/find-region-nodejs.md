---
title: Hitta THOMAS region med Node.js inom gränserna för språket förstå (THOMAS) | Microsoft Docs
description: Programmässigt hitta publicera region med slutpunkten nyckeln och program-ID för THOMAS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 6d85e6007b3e85a1b55997541e721ad57c22dddf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111753"
---
# <a name="region-can-be-determined-from-api-call"></a>Region kan fastställas från API-anrop 
Om du har THOMAS app-ID och THOMAS prenumerations-ID, kan du hitta vilken region som ska användas för slutpunkt-frågor.

> [!NOTE] 
> Den fullständiga lösningen till Node.js är tillgänglig från den [ **THOMAS prover** Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>THOMAS endpoint frågan strategi
Varje THOMAS endpoint-fråga kräver:

* En slutpunktsnyckel
* Ett app-ID
* En region

Om THOMAS endpoint frågan använder korrekt slutpunkt nyckel och app-ID men fel region, är svarskoden 401. 401 begäran räknas inte mot prenumerationens kvoter. Aktivera denna begäran till en strategi för att söka alla regioner för att hitta rätt region. Rätt region är endast begäran som returnerar en 2xx statuskod. 

|Svarskod|Parametrar|
|--|--|
|2xx|rätt slutpunktsnyckel<br>Korrigera app-ID<br>rätt värden region|
|401|rätt slutpunktsnyckel<br>Korrigera app-ID<br>_felaktig_ värden region|

## <a name="nodejs-code-to-find-region"></a>Node.js-kod för att söka efter region
Konsolprogrammet tar THOMAS app-ID och nyckeln slutpunkt och returnerar alla regioner som är kopplade till den. För närvarande en slutpunktsnyckel har skapats efter region så att endast en region ska returnera.

Inkludera NPM-beroenden:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Lägg till konstanter. Ersätt variabelvärden för `subscriptionKey` och `appId` med egna värden.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Lägg till `searchRegions` funktion för att hitta regionen. Alla felaktiga regioner returnera 401, som är fångas och ignoreras.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Anropa den `searchRegions` fungerar och returnera enskild region:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

När du kör programmet visar terminalen regionen för app-ID.

![Skärmbild av konsolapp visar THOMAS region](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Nästa steg

Mer information om THOMAS [regioner](luis-reference-regions.md).
