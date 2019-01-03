---
title: Slutpunkten regionC#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Med C#, hitta publicera region med slutpunktsnyckeln och program-ID för LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: f95dec8a539a92a0397421fbde411f646eeca3ca
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720092"
---
# <a name="programmatically-find-endpoint-region-with-c"></a>Hitta endpoint region med programmässigtC# 
Om du har LUIS app-ID och prenumerations-ID LUIS kan du vilken region du ska använda för slutpunkten frågor.

> [!NOTE] 
> Den fullständiga C# lösning kan hämtas från den [ **Azure-Samples** GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>C#-klasskod för att hitta region
Konsolprogrammet tar LUIS app-ID och slutpunktsnyckeln och returnerar alla regioner som associeras med den. För närvarande en slutpunktsnyckeln skapas per region så att endast en region ska returnera.

Inkludera beroenden för .net-biblioteket:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Lägg till den här anpassade LUIS-klassen för att hitta regionen. Ersätt variabelvärden för `luisAppId` och `luisSubscriptionKey` med dina egna värden. Alla regioner som returnerar 401 skrivs till Felsökningskonsolen. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Det här är ett exempel på anrop av den anpassade LUIS-klassen i konsolprogrammets Main-metoden:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

När programmet körs i konsolen kan se regionen för app-ID.

![Skärmbild av konsolapp som visar LUIS region](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Nästa steg

Mer information om LUIS [regioner](luis-reference-regions.md).
