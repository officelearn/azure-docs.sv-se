---
title: Modellering steget i Team Data Science Process-livscykel – Azure | Microsoft Docs
description: Mål, uppgifter och slutprodukter för modellering steg i dina data science-projekt
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 4043ae4cf8ab7d9716db834fed0019456f2a52d4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227543"
---
# <a name="modeling"></a>Modellering

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med modellering scenen för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Förståelse för verksamheten**
   2. **Data förvärv och förståelse av**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livscykeln:

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Fastställa optimal datafunktioner för machine learning-modellen.
* Skapa en informativa machine learning-modell som beräknar målet så exakt som möjligt.
* Skapa en machine learning-modell som passar för produktion.

## <a name="how-to-do-it"></a>Gör så
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

  * **Funktionstekniker**: skapa datafunktioner från rådata för att underlätta modellträning.
  * **Modellera utbildning**: hitta modellen som svar på frågan så exakt som möjligt genom att jämföra deras framgångsmått.
  * Avgöra om din modell är **lämplig för produktion.**

### <a name="feature-engineering"></a>Funktionstekniker
Funktionsframställning innebär att de ska ingå, aggregering och omvandling av rådata variabler för att skapa funktioner som används i analysen. Om du vill att inblick i vad som aktiverar en modell, måste du förstå hur funktionerna som är relaterade till varandra och hur machine learning-algoritmer är att använda dessa funktioner. 

Det här steget kräver en creative kombination av expertis och de insikter som hämtats från steget för utforskning av data. Funktionsframställning är en balansgång för att söka efter och inklusive informativa variabler, men samtidigt som försöker undvika att för många orelaterade variabler. Informativa variabler förbättra dina resultat. orelaterade variabler introducera onödiga bruset i modellen. Du måste också generera dessa funktioner för alla nya data som hämtades när bedömning. Generering av dessa funktioner kan därför bara beror på data som är tillgängliga vid tidpunkten för bedömning. 

Teknisk vägledning för funktionen tekniska när utnyttja olika Azure-datateknik finns i [Funktionstekniker i data science process](create-features.md). 

### <a name="model-training"></a>Modellträning
Beroende på vilken typ av fråga som du försöker att besvara finns det många modellering algoritmer. Anvisningar om hur du väljer algoritmerna finns i [så väljer du algoritmer för Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Även om den här artikeln använder Azure Machine Learning kan är de riktlinjer som den innehåller användbart för eventuella machine learning-projekt. 

Processen för att modellen innehåller följande steg: 

   * **Dela upp indata** slumpmässigt för modellering i en datauppsättning för träning och en datauppsättning för testning.
   * **Skapa modeller** med hjälp av datauppsättning för utbildning.
   * **Utvärdera** utbildningen och test-datauppsättning. Använda en serie konkurrerande machine learning-algoritmer tillsammans med olika parametrar som tillhörande justering (kallas en *parameterrensning*) som är inriktat mot besvarar frågan med aktuella data.
   * **Fastställa vilken lösning som ”bästa”** försöka besvara frågan genom att jämföra framgångsmått mellan alternativa metoder.

> [!NOTE]
> **Undvika läckage**: Om du inkluderar data utanför datauppsättningen utbildning som gör att en modell eller en maskininlärningsalgoritm att göra orealistiskt bra förutsägelser kan det uppstå dataläckage. Läckage är en vanlig orsak till varför data forskare få vad när de får förebyggande resultat som verkar vara för bra ska vara sant. Dessa beroenden kan vara svåra att identifiera. För att undvika läckage ofta kräver att iterera mellan att skapa en datauppsättning för analys, skapa en modell och utvärderar noggrannare resultat. 
> 
> 

Vi tillhandahåller en [automatiserad modellering och Rapporteringsverktyg](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) med TDSP som kan gå igenom flera algoritmer och parametern Svep för att skapa en baslinje-modell. Den ger också en baslinje modellering rapport som sammanfattar prestanda för varje modell och parameterkombinationen inklusive variabeln prioritet. Den här processen är också iterativ eftersom det kan få ytterligare funktionsframställning. 

## <a name="artifacts"></a>Artefakter
Artefakter som skapats i den här fasen inkluderar:

   * [Egenskapsuppsättningar](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): funktioner som har utvecklats för modellering beskrivs den **egenskapsuppsättningar** delen av den **datadefinition** rapporten. Det finns länkar till koden för att generera funktionerna och en beskrivning av hur funktionen skapades.
   * [Modellera rapporten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): för varje modell som testas, en standard-mallbaserade rapport som innehåller information om varje experiment skapas.
   * **Kontrollpunkt beslut**: utvärdera om modellen presterar bra för att distribuera den till ett produktionssystem. Vissa viktiga frågor är:
     * Modellen besvara frågan med tillräckligt med tillförsikt beroende testdata? 
     * Bör du försöka eventuella alternativa metoder? Ska du samla in ytterligare data, göra mer funktionsframställning eller experimentera med andra algoritmer?

## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://aka.ms/datascienceprocess). 
