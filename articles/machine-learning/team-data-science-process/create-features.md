---
title: Egenskapsval i datavetenskap | Microsoft Docs
description: "Beskrivs i syfte att funktionen tekniker ger exempel på sin roll i förbättring av data för maskininlärning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: c0933fec67bbcfc2a8c450c1e2472440cf38f338
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="feature-engineering-in-data-science"></a>Egenskapsval i datavetenskap
Det här avsnittet beskrivs syftet med funktionen tekniker och exempel på sin roll i förbättring av data för maskininlärning. I exemplen som används för att illustrera den här processen hämtas från Azure Machine Learning Studio. 

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Detta **menyn** länkar till avsnitt som beskriver hur du skapar funktioner för data i olika miljöer. Den här uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Funktionen engineering försöker öka förutsägande kraften i learning algoritmer genom att skapa funktioner från rådata som underlättar learning-processen. Tekniker och funktioner som är en del av TDSP som beskrivs i den [vad är livscykeln Team av vetenskapliga data?](overview.md) Funktionen tekniker och markeringen är delar av den **utveckla funktioner** steg i TDSP. 

* **egenskapsval**: den här processen försöker att skapa ytterligare relevanta funktioner från befintliga raw-funktioner i data och för att öka förutsägande kraften i Inlärningsalgoritmen.
* **funktionen val**: den här processen väljer viktiga delmängd av den ursprungliga datafunktioner i ett försök att minska dimensionaliteten för utbildning-problem.

Normalt **egenskapsval** används först för att generera ytterligare funktioner, och sedan den **funktion markeringen** steg utförs för att ta bort irrelevanta, redundant eller hög korrelerade funktioner.

Utbildning data som används i machine learning kan ofta förbättras av extrahering av funktioner från rådata som samlas in. Ett exempel på en bakåtkompilerade funktion i samband med att lära dig hur du klassificerar avbildningar av handskriven tecken är skapandet av lite densitet kartan konstrueras från rådata bitars distribution data. Den här kartan kan hjälpa dig att hitta kanterna på tecknen effektivare än att bara använda raw distribution direkt.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="creating-features-from-your-data---feature-engineering"></a>Skapa funktioner från dina Data - Egenskapsval
Utbildning data består av en matris som består av exempel (poster eller observationer som lagras i rader) som har en uppsättning funktioner (variabler eller fält som lagras i kolumner). Funktioner som anges i experimentets utformning förväntas karaktäriserar mönster i data. Även om många av rådata fälten kan inkluderas direkt i den valda funktionen som angivits för tränar en modell, men det är ofta fallet att ytterligare (bakåtkompilerade) funktioner måste konstrueras från funktionerna i rådata för att generera en förbättrad utbildning datauppsättning.

Vilken typ av funktioner som ska skapas för att förbättra datauppsättningen när en modell? Bakåtkompilerade funktioner som förbättrar utbildning ger information som särskiljer bättre mönster i data. Vi räknar med de nya funktionerna som innehåller ytterligare information som inte är tydligt avbildade eller enkelt tydligt i funktionsuppsättningen ursprungliga eller en befintlig. Men den här processen är något som en bild. Ljud-och produktiva kräver ofta vissa expertis.

När du startar med Azure Machine Learning är det enklast att tag i den här processen concretely med exemplen i Studio. Här visas två exempel:

* Ett exempel på regression [förutsägelse av antalet cykel hyra](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) i ett övervakat experiment där målvärden känt
* En text utvinningsmodellen klassificering exempel med [hash-funktionen](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-adding-temporal-features-for-regression-model"></a>Exempel 1: Lägga till Temporala funktioner för regressionsmodell
Nu ska vi använda den experiment ”begäran prognoser för cyklar” i Azure Machine Learning Studio för att demonstrera hur du utveckla funktioner för en regression aktivitet. Syftet med experimentet är att förutsäga behovet av cyklar, det vill säga antalet cykel hyra inom en specifik månad/dag/timme. Dataset ”cykel uthyrning UCI dataset” används som indata rådata. Den här datauppsättningen baseras på verkliga data från det kapital Bikeshare företag som upprätthåller en cykel uthyrning nätverk i Washington i USA DC. Dataset representerar antalet cykel hyra inom en viss timme per dag under år 2011 och år 2012 och innehåller 17379 rader och kolumner som 17. Funktionsuppsättningen rådata innehåller väder (temperatur/fuktighet/vindhastighet) och vilken typ av dag (helgdag/veckodag). Fältet för att förutsäga är ”cnt”, en uppräkning som representerar cykel hyra inom en viss timme och vilka adressintervall sträcker sig från 1 till 977.

Med målet att konstruera effektiva funktioner i träningsinformationen fyra regression modeller skapas med samma algoritm men med fyra olika utbildning datauppsättningar. Fyra datauppsättningar representerar samma inkommande rådata, men med ett ökande antal funktioner. Dessa funktioner grupperas i fyra kategorier:

1. A = väder helgdag + veckodag + helgens funktioner för förväntade dagen
2. B = antalet cyklar har hyrs i var och en av de föregående 12 timmarna
3. C = antalet cyklar har hyrs i var och en av de senaste 12 dagarna på samma timme
4. D = antal cyklar har hyrs i varje föregående 12 veckor på samma timme och samma dag

Förutom funktionen uppsättning A som redan finns i den ursprungliga rådata, skapas de andra tre olika funktioner via funktionen teknisk process. Funktionsuppsättning B insamlingar mycket färsk begäran för cyklarna. Funktionen Ange C insamlingar behovet av cyklar i en viss timme. D insamlingar begäran för cyklar funktionsuppsättning på särskilda timme och viss dag i veckan. Fyra utbildning datauppsättningar varje innehåller funktionen uppsättning A, A + B, A + B + C och A + B + C + D.

Dessa fyra utbildning datauppsättningar bildas i Azure Machine Learning-experiment via fyra grenar från förbearbetade inkommande dataset. Förutom vänster de flesta gren, var och en av dessa filialer innehåller en [köra R-skriptet](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modul, som är en uppsättning härledda funktioner (funktionsuppsättning B och C D) respektive skapas och läggs till i importerade dataset. Följande bild visar R-skriptet som används för att skapa funktionsuppsättningen B i andra vänstra grenen.

![skapa funktioner](./media/create-features/addFeature-Rscripts.png)

Jämförelse av de fyra modellerna prestandaresultat sammanfattas i följande tabell. Bästa resultat visas funktionerna A + B + C. Observera att Felfrekvensen minskar när ytterligare funktioner ingår i utbildning-data. Verifierar våra antagandet att funktionsuppsättningen B och C ger ytterligare relevant information för aktiviteten regression. Men att lägga till funktionen D verkar inte ange några ytterligare minskning i frekvens för fel.

![jämförelse av resultat](./media/create-features/result1.png)

## <a name="example2"></a>Exempel 2: Skapa funktioner i texten datautvinning
Funktionen tekniker används ofta i uppgifter som rör text utvinningsmodellen, till exempel dokumentet klassificering och sentiment analys. Till exempel när vi vill klassificera dokument i flera kategorier är vanliga antaganden att word/fraser som ingår i en doc-kategori mindre troligt att ske i en annan doc-kategori. Frekvensen av ord/fraser distribution kan med andra ord att beskriva olika kategorierna. I texten utvinningsmodellen program, eftersom enskilda delar av text-innehållet är vanligtvis indata, behövs funktionen tekniska processen för att skapa funktioner som rör /-fras frekvenser.

För att uppnå den här uppgiften, en teknik som kallas **hash-funktionen** används för att aktivera effektivt godtycklig textfunktioner i index. I stället för att associera varje text-funktion (ord/fraser) till ett visst index, den här metoden fungerar genom att en hash-funktion funktioner och använda deras hashvärden som index direkt.

I Azure Machine Learning finns en [funktions-hashning](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul som skapar dessa /-fras funktioner lätt. Följande bild visar ett exempel på hur du använder den här modulen. Inkommande datamängden innehåller två kolumner: den book klassificering mellan 1 och 5, och det faktiska granska innehållet. Syftet med detta [funktions-hashning](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modulen är att hämta flera nya funktioner som visar frekvensen förekomsten av motsvarande ord / fraser används i viss bok granska. Vi vill använda den här modulen gör du följande:

* Först markera den kolumn som innehåller indatatexten (”Col2” i det här exemplet).
* Andra, Ställ in ”Hashing bitsize” på 8, vilket innebär att 2 ^ 8 = 256 funktioner kommer att skapas. Word/fas i all text ska kodas till 256 index. Parametern ”Hashing bitsize” intervall mellan 1 och 31. Ord och fraser används är mindre troligt att kodas till samma index, om det ska vara ett större antal.
* Det tredje värdet parametern ”N gram” 2. Detta får frekvensen förekomsten av unigrams (en funktion för varje ord) och bigrams (en funktion för varje par av intilliggande ord) från indatatexten. Parametern ”N gram”-intervall från 0 till 10 som anger det maximala antalet sekventiella ord som ska ingå i en funktion.  

![”Funktion Hashing” modul](./media/create-features/feature-Hashing1.png)

Följande bild visar hur dessa nya funktion utseende t.ex.

![”Funktion Hashing” exempel](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Slutsats
Bakåtkompilerade och valda funktioner ökar effektiviteten för utbildning-processen att extrahera den viktiga informationen i data. De kan också förbättra kraften hos dessa modeller att klassificera indata korrekt och att förutsäga mer robustly resultat av intresse. Funktionen tekniker och val kan också kombinera om du vill göra learning mer beräkningsmässigt tractable. Den gör detta genom att öka och minska antalet funktioner som behövs för att kalibrera eller tränar en modell. Funktioner som markerats för att träna modellen är matematiskt sett en minimal uppsättning oberoende variabler som beskriver mönster i data och förutsäga resultat har.

Observera att det inte alltid är att utföra Funktionsurval teknik eller funktion. Om det behövs eller inte beror på de data som vi har eller samla in och algoritmen vi väljer mål för experimentet.

