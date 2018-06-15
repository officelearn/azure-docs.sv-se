---
title: Hitta THOMAS region med C# inom gränserna för språket förstå (THOMAS) | Microsoft Docs
description: Programmässigt hitta publicera region med prenumerationen nyckeln och program-ID för THOMAS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2018
ms.locfileid: "35355982"
---
# <a name="region-can-be-determined-from-api-call"></a>Region kan fastställas från API-anrop 
Om du har THOMAS app-ID och THOMAS prenumerations-ID, kan du hitta vilken region som ska användas för slutpunkt-frågor.

> [!NOTE] 
> Den fullständiga C#-lösningen är tillgänglig från den [ **THOMAS prover** Github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>C# kod efter region
Konsolprogrammet tar THOMAS app-ID och nyckeln för prenumeration och returnerar alla regioner som är kopplade till den. För närvarande en nyckel i prenumerationen skapas efter region så att endast en region ska returnera.

Innehåller beroenden för .net-bibliotek:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Lägg till den här anpassade THOMAS klassen för att hitta regionen. Ersätt variabelvärden för `luisAppId` och `luisSubscriptionKey` med egna värden. Alla regioner som returnerar 401 ska skrivas till Felsökningskonsolen. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Detta är ett exempel på anrop av anpassad THOMAS klass i konsolen programmets Main-metoden:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

När programmet körs, visar konsolen region för nyckeln för app-ID och din prenumeration.

![Skärmbild av konsolapp visar THOMAS region](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Nästa steg

Mer information om THOMAS [regioner](luis-reference-regions.md).
