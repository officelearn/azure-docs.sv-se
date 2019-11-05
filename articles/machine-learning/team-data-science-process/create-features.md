---
title: Funktions teknik i data vetenskap – team data science process
description: Förklarar syftet med funktions teknik och ger exempel på dess roll i data förbättrings processen för Machine Learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e633c5742b8a7882149a347ced46e55440cb6913
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492444"
---
# <a name="feature-engineering-in-data-science"></a>Funktions teknik i data vetenskap
Den här artikeln förklarar syftet med funktions teknik och innehåller exempel på dess roll i processen för data förbättring av Machine Learning. De exempel som används för att illustrera den här processen hämtas från Azure Machine Learning Studio. 

Den här uppgiften är ett steg i [TDSP (Team data science process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Funktions teknikerna försöker öka förutsägelse kraften hos Learning-algoritmer genom att skapa funktioner från rå data som underlättar inlärnings processen. Teknikerna och valet av funktioner är en del av TDSP som beskrivs i [livs cykeln för team data science process?](overview.md) Funktions teknik och urval är delar av steget **utveckla funktioner** i TDSP. 

* **funktions teknik**: den här processen försöker skapa ytterligare relevanta funktioner från befintliga obearbetade funktioner i datan och öka den förutsägande kraften hos inlärnings algoritmen.
* **Val av funktion**: den här processen väljer en nyckel del av de ursprungliga data funktionerna i ett försök att minska inlärnings problemets dimensionalitet.

Normalt **används funktionerna först** för att generera ytterligare funktioner och sedan utförs **funktions urvalet** för att eliminera irrelevanta, redundanta eller mycket korrelerade funktioner.

De tränings data som används i Machine Learning kan ofta förbättras genom extrahering av funktioner från rå data som samlas in. Ett exempel på en funktion som är utformad i sammanhang för att lära dig hur du klassificerar bilder av handskrivna tecken är att skapa en bit Täthets karta som är konstruerad från rå bit distributions data. Den här kartan kan hjälpa dig att hitta kanterna på tecknen mer effektivt än att bara använda den råa fördelningen direkt.

Information om hur du skapar funktioner för data i vissa miljöer finns i följande artiklar:

* [Skapa funktioner för data i SQL Server](create-features-sql-server.md)
* [Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Skapa funktioner från din data-funktions teknik
Tränings data består av en matris som består av exempel (poster eller observationer lagrade i rader), som var och en har en uppsättning funktioner (variabler eller fält som lagras i kolumner). De funktioner som anges i experiment designen förväntas karakterisera mönstren i data. Även om många av rå data fälten kan inkluderas direkt i den valda funktions uppsättningen som används för att träna en modell, är det ofta fallet att ytterligare (tillverkade) funktioner måste konstrueras från funktionerna i rå data för att generera en förbättrad utbildnings data uppsättning.

Vilken typ av funktioner ska skapas för att förbättra data uppsättningen vid inlärning av en modell? Tekniker som förbättrar utbildningen ger information som bättre särskiljer mönstren i data. De nya funktionerna förväntas ge ytterligare information som inte tydligt fångas in eller som lätt syns i den ursprungliga eller befintliga funktions uppsättningen. Men den här processen är något av en bild. Ett ljud-och produktivt beslut kräver ofta vissa domän kunskaper.

När du börjar med Azure Machine Learning är det enklast att grepp den här processen med hjälp av exempel som tillhandahålls i Studio. Två exempel visas här:

* Ett Regressions exempel som [förutsäger antalet cykel hyror](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) i ett övervakat experiment där mål värden är kända
* Ett exempel på en text utvinnings klassificering som använder [funktionen hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exempel 1: Lägg till temporala funktioner för en Regressions modell
Vi använder experimentet "prognoser för efter frågan av cyklar" i Azure Machine Learning Studio (klassisk) för att demonstrera funktioner för en Regressions uppgift. Syftet med det här experimentet är att förutse efter frågan på cyklarna, det vill säga antalet cykel uthyrningar inom en bestämd månad/dag/timme. Data uppsättningen "cykel uthyrnings-data uppsättning" används som rå data för indata. Den här data uppsättningen baseras på verkliga data från kapitalet Bikeshare-företaget som underhåller ett cykel uthyrnings nätverk i Washington DC i USA. Data uppsättningen representerar antalet cykel uthyrningar inom en angiven timme på en dag i år 2011 och år 2012 och innehåller 17379-rader och 17 kolumner. RAW-funktions uppsättningen innehåller väder förhållanden (temperatur/fuktighet/vindhastighet) och typ av dag (helgdag/veckodag). Det fält som ska förutsäga är antalet "CNT" som representerar cykel hyrarna inom en angiven timme och vilka sträcker sig från 1 till 977.

Med målet att konstruera effektiva funktioner i tränings data skapas fyra Regressions modeller med samma algoritm, men med fyra olika inlärnings data uppsättningar. De fyra data uppsättningarna representerar samma rå data, men med ett ökande antal funktioner. Dessa funktioner är grupperade i fyra kategorier:

1. A = väder + helg + helg + veckosluts funktioner för den förväntade dagen
2. B = antalet cyklar som har hyrs under de föregående 12 timmarna
3. C = antal cyklar som har hyrs under de föregående 12 dagarna i samma timme
4. D = antal cyklar som hyrs under de närmast föregående 12 veckorna i samma timme och samma dag

Förutom feature set A, som redan finns i de ursprungliga rå data, skapas de andra tre uppsättningarna med funktioner i funktions teknik processen. Funktions uppsättning B fångar upp mycket senaste efter frågan på cyklarna. Funktions uppsättning C fångar efter frågan för cyklar vid en viss timme. Funktionen D samlar in efter frågan för cyklar vid en viss tid och en viss dag i veckan. De fyra inlärnings data uppsättningarna omfattar en funktion som har angetts A, A + B, A + B + C och A + B + C + D.

I Azure Machine Learning experimentet skapas dessa fyra utbildnings data uppsättningar via fyra grenar från den förbehandlade indata-datauppsättningen. Förutom grenen längst till vänster innehåller var och en av dessa grenar en modul för att [köra R-skript](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) där de härledda funktionerna (funktions uppsättning B, C och D) skapas och läggs till i den importerade data uppsättningen. Följande bild visar det R-skript som används för att skapa funktions uppsättning B i den andra vänstra grenen.

![skapa funktioner](./media/create-features/addFeature-Rscripts.png)

En jämförelse av prestanda resultatet för de fyra modellerna sammanfattas i följande tabell: 

![resultat jämförelse](./media/create-features/result1.png)

De bästa resultaten visas av funktionerna A + B + C. Observera att fel frekvensen minskar när ytterligare funktions uppsättning ingår i tränings data. Den verifierar att funktionen uppsättning B, C ger ytterligare relevant information för Regressions uppgiften. Men om du lägger till D-funktionen verkar det inte ge någon ytterligare minskning av fel frekvensen.

## <a name="example2"></a>Exempel 2: skapa funktioner i text utvinning
Funktions teknik används ofta i uppgifter som rör text utvinning, till exempel dokument klassificering och sentiment analys. Om du till exempel vill klassificera dokument i flera kategorier, är ett typiskt antagande att ord/fraser som ingår i en dokument kategori är mindre sannolika i en annan dokument kategori. Med andra ord kan frekvensen för fördelningen av ord/fraser kunna karakterisera olika dokument kategorier. I text utvinnings program, eftersom enskilda delar av text innehåll vanligt vis fungerar som indata, krävs funktions tekniks processen för att skapa funktioner som involverar frekvenser för ord/fraser.

För att uppnå den här uppgiften används en teknik som kallas **funktion-hash** för att effektivt aktivera godtycklig text funktioner i index. I stället för att associera varje text funktion (ord/fraser) till ett visst index, fungerar den här metoden genom att använda en hash-funktion för funktionerna och använda sina hash-värden som index direkt.

I Azure Machine Learning finns det en modul för [hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) av funktioner som skapar dessa ord/fras-funktioner bekvämt. Följande bild visar ett exempel på hur du använder den här modulen. Data uppsättningen för indata innehåller två kolumner: bok klassificeringen sträcker sig från 1 till 5 och det faktiska gransknings innehållet. Målet med den här modulens [hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -modul är att hämta en massa nya funktioner som visar förekomst frekvensen för motsvarande Word-/phrase i en viss bok granskning. Utför följande steg för att använda den här modulen:

* Börja med att markera den kolumn som innehåller indatamängden ("Col2" i det här exemplet).
* Ange sedan "hashing bitsize" till 8, vilket innebär att 2 ^ 8 = 256 funktioner skapas. Ordet/fasen i all text kommer att hashas till 256-index. Parametern "hashing bitsize" sträcker sig från 1 till 31. Ord/phrase (s) är mindre sannolika att hashas i samma index om det ställs in som ett större tal.
* För det tredje anger du parametern "N-g" till 2. Det här värdet hämtar förekomst frekvensen för unigrams (en funktion för varje enskilt ord) och två g (en funktion för varje par av intilliggande ord) från den inmatade texten. Parametern "N-gram" sträcker sig från 0 till 10, vilket anger det maximala antalet sekventiella ord som ska tas med i en funktion.  

![Modulen "funktion-hashing"](./media/create-features/feature-Hashing1.png)

Följande bild visar hur den nya funktionen ser ut.

![Exempel på funktions-hashing](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Slutsats
Utformade och valda funktioner ökar effektiviteten i inlärnings processen, som försöker extrahera nyckelinformation som finns i data. De kan också förbättra kraften i dessa modeller för att klassificera indata på ett korrekt sätt och förutsäga resultat av intresse mer robust. Funktions teknik och urval kan också kombineras för att göra inlärningen mer beräknings bara. Det gör det genom att förbättra och minska antalet funktioner som behövs för att kalibrera eller träna en modell. De funktioner som har valts för att träna modellen är en minimal uppsättning oberoende variabler som förklarar mönstren i data och förutsäger sedan resultatet.

Det är inte alltid nödvändigt att utföra funktion teknik eller val av funktioner. Oavsett om det behövs eller inte beror på vilka data som är tillgängliga eller insamlade, vilken algoritm som valts och syftet med experimentet.

