---
title: "Modeling steget i teamet datavetenskap processen livscykel – Azure | Microsoft Docs"
description: "Mål, uppgifter och leveranser för modellering steg i dina datavetenskap projekt."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>Modellering

Det här avsnittet beskriver mål, uppgifter, och leveranser som är associerade med den **modeling steget** processens Team datavetenskap. Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

* **Så här fungerar för företag**
* **Datainsamling och förstå**
* **Modeling**
* **Distribution**
* **Kundens godkännande**

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Optimal datafunktioner för machine learning-modellen.
* En informativ ML-modell som beräknar målet bäst.
* En ML-modell som passar för produktion.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns tre huvudsakliga uppgifter som beskrivs i det här steget:

* **Egenskapsval**: skapa datafunktioner från rådata för att underlätta modellen utbildning.
* **Modellen utbildning**: hitta modellen som svar på frågan bäst genom att jämföra deras framgångsmått.
* Om din modell är **lämpar sig för produktion**.

### <a name="31-feature-engineering"></a>3.1 egenskapsval
Funktionen tekniker omfattar inkludering, sammanställning och omvandling av rådata variabler för att skapa funktioner som används i analysen. Om du vill inblick i vad som aktiverar en modell, måste du förstå hur funktioner som är relaterade till varandra och hur maskininlärningsalgoritmer som använder dessa funktioner. Det här steget kräver en kreativa kombination av domän kunskaper och insikter som erhållits från data från kartläggning av naturresurser steg. Det här är en balansgång för att söka efter och informativa variabler, inklusive samtidigt för många orelaterade variabler. Informativa variabler förbättra våra resultat. orelaterade variabler introducera onödiga störningar i modellen. Du måste också att generera dessa funktioner för alla nya data som hämtades när den bedömningen. Generering av dessa funktioner kan därför endast beror på data som är tillgängliga vid tidpunkten för resultatfunktioner. Teknisk information om funktionen tekniker när du använder olika tekniker för Azure data finns [Egenskapsval i datavetenskap processen](create-features.md). 

### <a name="32-model-training"></a>3.2 träning
Beroende på typ av frågan som du försöker besvara finns det många modellering algoritmer. Anvisningar om att välja algoritmer finns [så väljer du algoritmer för Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Även om den här artikeln är avsedd för Azure Machine Learning, är de riktlinjer som ger användbart för alla projekt för maskininlärning. 

Processen för modellen utbildning omfattar följande steg: 

* **Dela indata** slumpmässigt för modellering i en datauppsättning för träning och en test-datamängd.
* **Skapa modeller** med hjälp av datauppsättning för träning.
* **Utvärdera** (träning och testning dataset) en serie konkurrerande maskininlärningsalgoritmer tillsammans med de olika associerade justera parametrar (kallas även parametern Svep) som är riktade mot besvaras av intresse med aktuella data.
* **Bestämma vilken lösning som ”bästa”** att besvara frågan genom att jämföra lyckade mått mellan alternativa metoder.

> [!NOTE]
> **Undvika läckage av**: läckage av Data kan orsakas av inkludering av data utanför utbildning datamängden som gör att en modell eller maskininlärningsalgoritmen att göra förutsägelser för orealistiskt bra. Läckage är en vanlig orsak till varför data forskare få vad när de får förutsägande resultat som verkar vara för bra ska vara sant. Dessa beroenden kan vara svårt att identifiera. För att undvika läckage av ofta kräver iterera mellan bygga en datauppsättning för analys, skapa en modell och utvärdera noggrannhet. 
> 
> 

Vi tillhandahåller en [automatisk modellering och rapportering verktyget](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) med TDSP som kan köras via flera algoritmer och parametern Svep för att skapa en baslinje-modell. Den ger också en baslinje för modellering rapporten sammanfattar prestanda för varje modell och parameterkombinationen inklusive variabeln vikten. Den här processen är också iterativ som den kan enheten ytterligare funktionen tekniker. 

## <a name="artifacts"></a>Artefakter
Artefakter som skapas i det här steget inkluderar:

* [**Egenskapsuppsättningar**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): funktioner som utvecklats för modellering beskrivs i den **funktionsuppsättningarna** avsnitt i den **datadefinitionen** rapporten. Den innehåller pekare till kod för att generera de funktioner och en beskrivning på hur funktionen har genererats.
* [**Modellen rapporten**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): mall-baserad rapport som innehåller information om varje experiment skapas för varje modell som testas, en standard.
* **Kontrollpunkt beslut**: utvärdera om modellen presterar bra tillräckligt distribuera den till ett produktionssystem. Vissa viktiga frågor är:
  * Modellen besvara frågan tillräckligt säkert sätt få testdata? 
  * Borde testa alla alternativa metoder: samla in ytterligare data, gör mer funktionen tekniker eller experimentera med andra algoritmer?

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för Team av vetenskapliga data:

* [1. Så här fungerar för företag](lifecycle-business-understanding.md)
* [2. Datainsamling och förstå](lifecycle-data.md)
* [3. Modeling](lifecycle-modeling.md)
* [4. Distribution](lifecycle-deployment.md)
* [5. Kundens godkännande](lifecycle-acceptance.md)

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar. 