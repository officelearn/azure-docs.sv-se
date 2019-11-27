---
title: Modellering steg i livscykeln för Team Data Science Process
description: Mål, uppgifter och slutprodukter för modellering steg i dina data science-projekt
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d72d39a2a59e06954c36473083af2d2b4689a7b6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538227"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modellering steg i livscykeln för Team Data Science Process

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med modellering scenen för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Affärs förståelse**
   2. **Data hämtning och förståelse**
   3. **Form**
   4. **Distribution**
   5. **Kund godkännande**

Här är en visuell representation av TDSP-livscykeln:

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Fastställa optimal datafunktioner för machine learning-modellen.
* Skapa en informativa machine learning-modell som beräknar målet så exakt som möjligt.
* Skapa en machine learning-modell som passar för produktion.

## <a name="how-to-do-it"></a>Gör så
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

  * **Funktions teknik**: skapa data funktioner från rå data för att under lätta modell träningen.
  * **Modell träning**: Leta upp den modell som svarar på frågan noggrant genom att jämföra deras framgång.
  * Ta reda på om din modell är **lämplig för produktion.**

### <a name="feature-engineering"></a>Funktionstekniker
Funktionsframställning innebär att de ska ingå, aggregering och omvandling av rådata variabler för att skapa funktioner som används i analysen. Om du vill att inblick i vad som aktiverar en modell, måste du förstå hur funktionerna som är relaterade till varandra och hur machine learning-algoritmer är att använda dessa funktioner. 

Det här steget kräver en creative kombination av expertis och de insikter som hämtats från steget för utforskning av data. Funktionsframställning är en balansgång för att söka efter och inklusive informativa variabler, men samtidigt som försöker undvika att för många orelaterade variabler. Informativa variabler förbättra dina resultat. orelaterade variabler introducera onödiga bruset i modellen. Du måste också generera dessa funktioner för alla nya data som hämtades när bedömning. Generering av dessa funktioner kan därför bara beror på data som är tillgängliga vid tidpunkten för bedömning. 

Teknisk vägledning om funktions teknik när du använder olika Azure-datatekniker finns i [funktions teknik i processen för data vetenskap](create-features.md). 

### <a name="model-training"></a>Modellträning
Beroende på vilken typ av fråga som du försöker att besvara finns det många modellering algoritmer. Anvisningar om hur du väljer algoritmer finns i [så här väljer du algoritmer för Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Även om den här artikeln använder Azure Machine Learning kan är de riktlinjer som den innehåller användbart för eventuella machine learning-projekt. 

Processen för att modellen innehåller följande steg: 

   * **Dela indata-data** slumpmässigt för modellering i en tränings data uppsättning och en test data uppsättning.
   * **Skapa modeller** med hjälp av övnings data uppsättningen.
   * **Utvärdera** utbildningen och test data uppsättningen. Använd en serie av konkurrerande algoritmer för maskin inlärning tillsammans med de olika associerade justerings parametrarna (kallas även *parameter svep*) som är kopplade till att besvara frågan om intresse med aktuella data.
   * **Fastställ den "bästa" lösningen** för att besvara frågan genom att jämföra måtten för framgång mellan alternativa metoder.

> [!NOTE]
> **Undvik läckage**: du kan orsaka dataläckage om du inkluderar data utanför den tränings data uppsättning som tillåter en modell eller en algoritm för maskin inlärning för att göra realistiska förutsägelser bättre. Läckage är en vanlig orsak till varför data forskare få vad när de får förebyggande resultat som verkar vara för bra ska vara sant. Dessa beroenden kan vara svåra att identifiera. För att undvika läckage ofta kräver att iterera mellan att skapa en datauppsättning för analys, skapa en modell och utvärderar noggrannare resultat. 
> 
> 

Vi tillhandahåller ett [automatiserat verktyg för modellering och rapportering](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) med TDSP som kan köras genom flera algoritmer och parameter rensningar för att skapa en bas linje modell. Den ger också en baslinje modellering rapport som sammanfattar prestanda för varje modell och parameterkombinationen inklusive variabeln prioritet. Den här processen är också iterativ eftersom det kan få ytterligare funktionsframställning. 

## <a name="artifacts"></a>Artefakter
Artefakter som skapats i den här fasen inkluderar:

   * [Funktions uppsättningar](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): de funktioner som har utvecklats för modellering beskrivs i avsnittet **funktions uppsättningar** i rapporten **data definition** . Det finns länkar till koden för att generera funktionerna och en beskrivning av hur funktionen skapades.
   * [Modell rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): för varje modell som provas skapas en standard-mall baserad rapport som innehåller information om varje experiment.
   * **Kontroll punkts beslut**: utvärdera om modellen fungerar tillräckligt väl för att distribuera den till ett produktions system. Vissa viktiga frågor är:
     * Modellen besvara frågan med tillräckligt med tillförsikt beroende testdata? 
     * Bör du försöka eventuella alternativa metoder? Ska du samla in ytterligare data, göra mer funktionsframställning eller experimentera med andra algoritmer?

## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Affärs förståelse](lifecycle-business-understanding.md)
   2. [Data hämtning och förståelse](lifecycle-data.md)
   3. [Form](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kund godkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
