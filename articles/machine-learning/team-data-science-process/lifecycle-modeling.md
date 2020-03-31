---
title: Modelleringsfasen för livscykeln för teamdatavetenskapsprocessen
description: Mål, uppgifter och slutprodukter för modelleringsfasen för dina datavetenskapliga projekt
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720477"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelleringsfasen för livscykeln för teamdatavetenskapsprocessen

I den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med modelleringsfasen i Team Data Science Process (TDSP). Den här processen innehåller en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapsprojekt. Livscykeln beskriver de stora faserna som projekt vanligtvis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP livscykel:

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Bestäm de optimala datafunktionerna för maskininlärningsmodellen.
* Skapa en informativ maskininlärningsmodell som förutsäger målet mest exakt.
* Skapa en maskininlärningsmodell som är lämplig för produktion.

## <a name="how-to-do-it"></a>Hur man gör det
Det finns tre huvuduppgifter som tas upp i detta skede:

  * **Funktionsteknik:** Skapa datafunktioner från rådata för att underlätta modellutbildning.
  * **Modellutbildning**: Hitta den modell som besvarar frågan mest exakt genom att jämföra deras framgångsmått.
  * Ta reda på om din modell är **lämplig för produktion.**

### <a name="feature-engineering"></a>Funktionstekniker
Funktionsteknik innebär inkludering, aggregering och omvandling av råvariabler för att skapa de funktioner som används i analysen. Om du vill ha insikt i vad som driver en modell måste du förstå hur funktionerna relaterar till varandra och hur maskininlärningsalgoritmerna ska använda dessa funktioner. 

Det här steget kräver en kreativ kombination av domänexpertis och de insikter som erhållits från datautforskningssteget. Funktionsteknik är en balansgång för att hitta och inkludera informativa variabler, men samtidigt försöka undvika alltför många orelaterade variabler. Informativa variabler förbättrar ditt resultat; icke-relaterade variabler inför onödigt brus i modellen. Du måste också generera dessa funktioner för alla nya data som erhållits under bedömning. Därför kan genereringen av dessa funktioner endast bero på data som är tillgängliga vid tidpunkten för bedömning. 

Teknisk vägledning om funktionsteknik när du använder olika Azure-datateknik finns [i Funktionsteknik i datascience-processen](create-features.md). 

### <a name="model-training"></a>Modellträning
Beroende på vilken typ av fråga du försöker besvara finns det många modelleringsalgoritmer tillgängliga. Information om hur du väljer algoritmer finns i [Så här väljer du algoritmer för Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Även om den här artikeln använder Azure Machine Learning är vägledningen den ger användbar för alla maskininlärningsprojekt. 

Processen för modellutbildning innehåller följande steg: 

   * **Dela indata** slumpmässigt för modellering i en träningsdatauppsättning och en testdatauppsättning.
   * **Skapa modellerna** med hjälp av träningsdatauppsättningen.
   * **Utvärdera** utbildningen och testdatauppsättningen. Använd en serie konkurrerande maskininlärningsalgoritmer tillsammans med de olika associerade justeringsparametrarna (så kallade *parametervepning)* som är inriktade på att besvara frågan om intresse med aktuella data.
   * **Bestäm den "bästa" lösningen** för att besvara frågan genom att jämföra framgångsmåtten mellan alternativa metoder.

> [!NOTE]
> **Undvik läckage:** Du kan orsaka dataläckage om du inkluderar data utanför träningsdatauppsättningen som gör att en modell eller maskininlärningsalgoritm kan göra orealistiskt bra förutsägelser. Läckage är en vanlig orsak till att dataforskare blir nervösa när de får prediktiva resultat som verkar för bra för att vara sanna. Dessa beroenden kan vara svåra att identifiera. För att undvika läckage kräver ofta att man itererar mellan att skapa en analysdatauppsättning, skapar en modell och utvärderar resultatens noggrannhet. 
> 
> 

Vi tillhandahåller ett [automatiserat modellerings- och rapporteringsverktyg](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) med TDSP som kan köras genom flera algoritmer och parametersvep för att producera en baslinjemodell. Den producerar också en baslinjemodelleringsrapport som sammanfattar prestanda för varje modell och parameterkombination, inklusive variabel prioritet. Denna process är också iterativ eftersom det kan driva ytterligare funktionen teknik. 

## <a name="artifacts"></a>Artefakter
De artefakter som produceras i detta skede inkluderar:

   * [Funktionsuppsättningar:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md)Funktionerna som utvecklats för modelleringen beskrivs i avsnittet **Funktionsuppsättningar** i **datadefinitionsrapporten.** Den innehåller pekare till koden för att generera funktioner och en beskrivning av hur funktionen genererades.
   * [Modellrapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): För varje modell som provas produceras en standardmallbaserad rapport som innehåller information om varje experiment.
   * **Kontrollpunktsbeslut**: Utvärdera om modellen fungerar tillräckligt för produktion. Några viktiga frågor att ställa är:
     * Besvarar modellen frågan med tillräcklig tillförsikt med tanke på testdata? 
     * Ska du prova några alternativa metoder? Ska du samla in ytterligare data, göra mer funktionsteknik eller experimentera med andra algoritmer?

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genomgångar från slutpunkt till slutpunkt som visar alla steg i processen för specifika scenarier. I artikeln [Exempelgenomgångar](walkthroughs.md) finns en lista över scenarier med länkar och miniatyrbeskrivningar. Genomgångarna illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [Använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
