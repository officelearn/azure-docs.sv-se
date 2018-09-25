---
title: Hitta endpoint region med Node.js i LUIS
titleSuffix: Azure Cognitive Services
description: Programmässigt hitta publicera region med slutpunktsnyckeln och program-ID för LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 193872d03dde1d5c620acca1b7aba99b60db238d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034084"
---
# <a name="find-endpoint-region-with-nodejs"></a>Hitta endpoint region med Node.js
Om du har LUIS app-ID och prenumerations-ID LUIS kan du vilken region du ska använda för slutpunkten frågor.

> [!NOTE] 
> Den fullständiga Node.js-lösningen finns i [**LUIS-Samples** Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>LUIS endpoint fråga strategi
Varje slutpunkt LUIS-fråga kräver:

* En slutpunktsnyckel
* Ett app-ID
* En region

Om LUIS endpoint frågan använder rätt slutpunkt nyckel och app-ID men fel region, är svarskoden 401. 401 begäran räknas inte mot Prenumerationskvoten. Omvandla den här begäran till en strategi för att söka i alla regioner för att hitta rätt region. Du rätt region är den enda begäran som returnerar ett 2xx statuskod. 

|Svarskod|Parametrar|
|--|--|
|2xx|rätt slutpunktsnyckel<br>rätt app-ID<br>rätt värden region|
|401|rätt slutpunktsnyckel<br>rätt app-ID<br>_felaktig_ värden region|

## <a name="nodejs-code-to-find-region"></a>Node.js-kod för att hitta region
Konsolprogrammet tar LUIS app-ID och slutpunktsnyckeln och returnerar alla regioner som associeras med den. För närvarande en slutpunktsnyckeln skapas per region så att endast en region ska returnera.

Inkludera NPM-beroenden:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Lägg till konstanter. Ersätt variabelvärden för `subscriptionKey` och `appId` med dina egna värden.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Lägg till `searchRegions` funktionen för att hitta regionen. Alla felaktiga regioner returnerar 401, som är fångas och ignoreras.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Anropa den `searchRegions` funktionen och returnerar en enda region:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

När programmet körs, terminalen kan se regionen för app-ID.

![Skärmbild av konsolapp som visar LUIS region](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Nästa steg

Mer information om LUIS [regioner](luis-reference-regions.md).
