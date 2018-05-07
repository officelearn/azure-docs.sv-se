---
title: Modeling fas i livscykeln Team datavetenskap Process - Azure | Microsoft Docs
description: Mål, uppgifter och leveranser för modellering steg i datavetenskap projekt
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 22f54e94293bae9f308b75f93e2dbec75b1f97c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="modeling"></a>Modellering

Den här artikeln beskrivs mål, uppgifter och leveranser som är kopplade till fasen modellering av Team Data vetenskap processen (TDSP). Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Så här fungerar för företag**
   2. **Datainsamling och förstå**
   3. **Modeling**
   4. **Distribution**
   5. **Kundens godkännande**

Här är en bild av TDSP livscykeln:

![TDSP livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Bestämma optimal datafunktioner för machine learning-modellen.
* Skapa en informativa machine learning-modell som beräknar målet bäst.
* Skapa en machine learning-modell som passar för produktion.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

  * **Egenskapsval**: skapa datafunktioner från rådata för att underlätta modellen utbildning.
  * **Modellen utbildning**: hitta modellen som svar på frågan bäst genom att jämföra deras framgångsmått.
  * Om din modell är **lämpar sig för produktion.**

### <a name="feature-engineering"></a>Funktionstekniker
Funktionen tekniker omfattar inkludering, sammanställning och omvandling av rådata variabler för att skapa funktioner som används i analysen. Om du vill inblick i vad som aktiverar en modell, måste du förstå hur funktionerna är relaterade till varandra och hur maskininlärningsalgoritmer som använder dessa funktioner. 

Det här steget kräver en kreativa kombination av domän kunskaper och insikter som erhållits från data från kartläggning av naturresurser steg. Funktionen tekniker är balansgång för att söka efter och informativa variabler, inklusive, men samtidigt försöka undvika att för många orelaterade variabler. Informativa variabler förbättra dina resultat. orelaterade variabler introducera onödiga störningar i modellen. Du måste också att generera dessa funktioner för alla nya data som hämtades när den bedömningen. Generering av dessa funktioner kan därför endast beror på data som är tillgängliga vid tidpunkten för resultatfunktioner. 

Teknisk information om funktionen engineering när Se för användning av olika tekniker för Azure data, se [Egenskapsval i datavetenskap processen](create-features.md). 

### <a name="model-training"></a>modell-utbildning
Beroende på vilken typ av fråga som du försöker besvara finns det många modellering algoritmer. Anvisningar om att välja algoritmer finns [så väljer du algoritmer för Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Även om den här artikeln används Azure Machine Learning, är de riktlinjer som ger användbar för maskininlärning projekt. 

Processen för modellen utbildning omfattar följande steg: 

   * **Dela indata** slumpmässigt för modellering i en datauppsättning för träning och en test-datamängd.
   * **Skapa modeller** med hjälp av datauppsättning för träning.
   * **Utvärdera** utbildning och test-datamängd. Använda en serie med konkurrerande maskininlärningsalgoritmer tillsammans med olika parametrar som associerade prestandajustering (kallas även en *parametern Svep*) som är riktade mot besvaras av intresse med aktuella data.
   * **Bestämma vilken lösning som ”bästa”** att besvara frågan genom att jämföra framgångsmått mellan alternativa metoder.

> [!NOTE]
> **Undvika läckage av**: Om du lägger till data utanför datauppsättning för träning som gör att en modell eller machine learning-algoritmen för att göra förutsägelser orealistiskt bra kan det uppstå läckage av data. Läckage är en vanlig orsak till varför data forskare få vad när de får förutsägande resultat som verkar vara för bra ska vara sant. Dessa beroenden kan vara svårt att identifiera. För att undvika läckage av ofta kräver iterera mellan bygga en datauppsättning för analys, skapa en modell och utvärdera korrektheten i resultatet. 
> 
> 

Vi tillhandahåller en [automatisk modellering och rapportering verktyget](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) med TDSP som kan köras via flera algoritmer och parametern Svep för att skapa en baslinje-modell. Den ger också en baslinje för modellering rapport som sammanfattar prestanda för varje modell och parameterkombinationen inklusive variabeln vikten. Den här processen är också iterativ som den kan enheten ytterligare funktionen tekniker. 

## <a name="artifacts"></a>Artefakter
Artefakter som skapas i det här steget inkluderar:

   * [Egenskapsuppsättningar](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): funktioner som utvecklats för modellering beskrivs i den **egenskapsuppsättningar** avsnitt i den **datadefinitionen** rapporten. Den innehåller pekare till kod för att generera funktionerna och en beskrivning av hur funktionen har genererats.
   * [Modellen rapporten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): mall-baserad rapport som innehåller information om varje experiment skapas för varje modell som testas, en standard.
   * **Kontrollpunkt beslut**: utvärdera om modellen presterar bra distribuera den till ett produktionssystem. Vissa viktiga frågor är:
     * Modellen besvara frågan tillräckligt säkert sätt få testdata? 
     * Bör du prova några alternativa lösningar? Bör du samla in ytterligare data, gör mer funktionen tekniker eller experimentera med andra algoritmer?

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Så här fungerar för företag](lifecycle-business-understanding.md)
   2. [Datainsamling och förstå](lifecycle-data.md)
   3. [Modeling](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundens godkännande](lifecycle-acceptance.md)

Vi ger fullständig slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgång](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar illustrerar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](http://aka.ms/datascienceprocess). 
