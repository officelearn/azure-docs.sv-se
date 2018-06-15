---
title: Hitta THOMAS region med Node.js inom gränserna för språket förstå (THOMAS) | Microsoft Docs
description: Programmässigt hitta publicera region med prenumerationen nyckeln och program-ID för THOMAS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 18ee324c10f074601c0c04573ca1a5266481f21c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2018
ms.locfileid: "35355985"
---
# <a name="region-can-be-determined-from-api-call"></a>Region kan fastställas från API-anrop 
Om du har THOMAS app-ID och THOMAS prenumerations-ID, kan du hitta vilken region som ska användas för slutpunkt-frågor.

> [!NOTE] 
> Den fullständiga lösningen till Node.js är tillgänglig från den [ **THOMAS prover** Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>THOMAS endpoint frågan strategi
Varje THOMAS endpoint-fråga kräver:

* En prenumeration för
* Ett app-ID
* En region

Om THOMAS endpoint frågan använder rätt prenumerations-ID för nyckeln och app men fel region, är svarskoden 401. 401 begäran räknas inte mot prenumerationens kvoter. Aktivera denna begäran till en strategi för att söka alla regioner för att hitta rätt region. Rätt region är endast begäran som returnerar en 2xx statuskod. 

|Svarskod|Parametrar|
|--|--|
|2xx|korrekt prenumeration nyckel<br>Korrigera app-ID<br>rätt värden region|
|401|korrekt prenumeration nyckel<br>Korrigera app-ID<br>_felaktig_ värden region|

## <a name="nodejs-code-to-find-region"></a>Node.js-kod för att söka efter region
Konsolprogrammet tar THOMAS app-ID och nyckeln för prenumeration och returnerar alla regioner som är kopplade till den. För närvarande en nyckel i prenumerationen skapas efter region så att endast en region ska returnera.

Inkludera NPM-beroenden:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Lägg till konstanter. Ersätt variabelvärden för `subscriptionKey` och `appId` med egna värden.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Lägg till `searchRegions` funktion för att hitta regionen. Alla felaktiga regioner returnera 401, som är fångas och ignoreras.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Anropa den `searchRegions` fungerar och returnera enskild region:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

När programmet körs, visar terminalen region för nyckeln för app-ID och din prenumeration.

![Skärmbild av konsolapp visar THOMAS region](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Nästa steg

Mer information om THOMAS [regioner](luis-reference-regions.md).