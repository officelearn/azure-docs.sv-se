---
title: Funktionsteknik inom datavetenskap - Team Data Science Process
description: Förklarar syftet med funktionsteknik och ger exempel på dess roll i dataförbättringsprocessen för maskininlärning.
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
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721140"
---
# <a name="feature-engineering-in-data-science"></a>Funktionsteknik inom datavetenskap
I den här artikeln beskrivs syftet med funktionsteknik och exempel på dess roll i dataförbättringsprocessen för maskininlärning. Exemplen som används för att illustrera den här processen hämtas från Azure Machine Learning Studio. 

Den här uppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Funktionsteknik försöker öka inlärningsalgoritmernas prediktiva kraft genom att skapa funktioner från rådata som underlättar inlärningsprocessen. Teknik och urval av funktioner är en del av TDSP som beskrivs i [livscykeln för Team Data Science Process?](overview.md) Funktionsteknik och val är delar av steget **Utveckla funktioner** i TDSP. 

* **funktionsteknik:** Den här processen försöker skapa ytterligare relevanta funktioner från de befintliga råfunktionerna i data och öka inlärningsalgoritmens prediktiva kraft.
* **funktionsval:** Den här processen väljer den viktigaste delmängden av ursprungliga datafunktioner i ett försök att minska dimensionaliteten i utbildningsproblemet.

Normalt **funktionsteknik** tillämpas först för att generera ytterligare funktioner, och sedan funktionen **urvalssteget** utförs för att eliminera irrelevanta, redundanta eller starkt korrelerade funktioner.

De utbildningsdata som används i maskininlärning kan ofta förbättras genom extrahering av funktioner från de insamlade rådata. Ett exempel på en konstruerad funktion i samband med att lära sig att klassificera bilder av handskrivna tecken är att skapa en bitdensitetskarta som är konstruerad från råbitdistributionsdata. Den här kartan kan hjälpa till att hitta kanterna på tecknen mer effektivt än att bara använda råfördelningen direkt.

Information om hur du skapar funktioner för data i specifika miljöer finns i följande artiklar:

* [Skapa funktioner för data i SQL Server](create-features-sql-server.md)
* [Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Skapa funktioner från dina data - funktionsteknik
Träningsdata består av en matris som består av exempel (poster eller observationer som lagras i rader), som var och en har en uppsättning funktioner (variabler eller fält som lagras i kolumner). De funktioner som anges i den experimentella designen förväntas karakterisera mönstren i data. Även om många av rådatafälten direkt kan inkluderas i den valda funktionsuppsättningen som används för att träna en modell, är det ofta så att ytterligare (konstruerade) funktioner måste konstrueras från funktionerna i rådata för att generera en förbättrad träningsdatauppsättning.

Vilken typ av funktioner bör skapas för att förbättra datauppsättningen när du tränar en modell? Konstruerade funktioner som förbättrar utbildningen ger information som bättre skiljer mönstren i data. De nya funktionerna förväntas ge ytterligare information som inte är tydligt fångad eller lätt att syna i den ursprungliga eller befintliga funktionsuppsättningen. Men denna process är något av en konst. Sunda och produktiva beslut kräver ofta viss domänexpertis.

När du börjar med Azure Machine Learning är det lättast att förstå den här processen konkret med hjälp av exempel som finns i Studion. Två exempel presenteras här:

* Ett regressionsexempel [Förutsägelse av antalet cykeluthyrning](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) i ett övervakat experiment där målvärdena är kända
* Ett exempel på klassificering av textbrytning med [funktions hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exempel 1: Lägga till tidsfunktioner för en regressionsmodell
Låt oss använda experimentet "Demand forecasting of bikes" i Azure Machine Learning Studio (klassiskt) för att visa hur du skapar funktioner för en regressionsuppgift. Syftet med detta experiment är att förutsäga efterfrågan på cyklar, det vill än antalet cykeluthyrning inom en viss månad / dag / timme. Datauppsättningen "Bike Rental UCI-datauppsättning" används som råindata. Denna datauppsättning är baserad på verkliga data från Capital Bikeshare företag som upprätthåller en cykel hyra nätverk i Washington DC i USA. Datauppsättningen representerar antalet cykeluthyrning inom en viss timme på dagen under åren 2011 och år 2012 och innehåller 17379 rader och 17 kolumner. Råfunktionen innehåller väderförhållanden (temperatur/luftfuktighet/vindhastighet) och dagens typ (semester/veckodag). Fältet att förutsäga är "cnt" räkna, som representerar cykeluthyrning inom en viss timme och som varierar från 1 till 977.

Med målet att konstruera effektiva funktioner i träningsdata byggs fyra regressionsmodeller med samma algoritm men med fyra olika träningsdatauppsättningar. De fyra datauppsättningarna representerar samma råa indata, men med ett ökande antal funktioner inställda. Dessa funktioner är grupperade i fyra kategorier:

1. A = väder + semester + veckodag + helgfunktioner för den förväntade dagen
2. B = antal cyklar som hyrdes under var och en av de föregående 12 timmarna
3. C = antal cyklar som hyrdes under var och en av de föregående 12 dagarna vid samma tidpunkt
4. D = antal cyklar som hyrdes under var och en av de föregående 12 veckorna vid samma timme och samma dag

Förutom funktionsuppsättning A, som redan finns i de ursprungliga rådata, skapas de andra tre uppsättningarna funktioner genom funktionsteknikprocessen. Funktionsuppsättning B fångar den senaste tidens efterfrågan på cyklarna. Funktionsuppsättning C fångar efterfrågan på cyklar vid en viss timme. Funktionsuppsättning D fångar efterfrågan på cyklar vid viss tidpunkt och viss veckodag. De fyra träningsdatauppsättningarna innehåller vardera funktionsuppsättningen A, A+B, A+B+C respektive A+B+C+D.

I Azure Machine Learning-experimentet bildas dessa fyra utbildningsdatauppsättningar via fyra grenar från den förbehandlade indatauppsättningen. Förutom grenen längst till vänster innehåller var och en av dessa grenar en [körning r-skriptmodul,](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) där de härledda funktionerna (funktionsuppsättningen B, C och D) respektive är konstruerade och bifogade till den importerade datauppsättningen. Följande bild visar R-skriptet som används för att skapa funktionsuppsättning B i den andra vänstra grenen.

![skapa funktioner](./media/create-features/addFeature-Rscripts.png)

En jämförelse av prestandaresultaten för de fyra modellerna sammanfattas i följande tabell: 

![jämförelse av resultat](./media/create-features/result1.png)

De bästa resultaten visas med funktionerna A+B+C. Felfrekvensen minskar när ytterligare funktionsuppsättning ingår i träningsdata. Den verifierar presumtionen att funktionsuppsättningen B, C ger ytterligare relevant information för regressionsuppgiften. Men att lägga till D-funktionen verkar inte ge någon ytterligare minskning av felprocenten.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>Exempel 2: Skapa funktioner i textutvinning
Funktionsteknik används i stor utsträckning i uppgifter som rör textbrytning, till exempel dokumentklassificering och sentimentanalys. När du till exempel vill klassificera dokument i flera kategorier är ett typiskt antagande att ord/fraser som ingår i en dokumentkategori är mindre benägna att inträffa i en annan dokumentkategori. Med andra ord kan frekvensen för ord-/frasfördelningen karakterisera olika dokumentkategorier. I text mining applikationer, eftersom enskilda bitar av text-innehåll vanligtvis fungerar som indata, funktionen engineering processen behövs för att skapa funktioner som involverar ord / fras frekvenser.

För att uppnå den här uppgiften används en teknik som kallas **funktionshämtning** för att effektivt omvandla godtyckliga textfunktioner till index. I stället för att associera varje textfunktion (ord/fraser) till ett visst index fungerar den här metoden genom att använda en hash-funktion på funktionerna och använda deras hash-värden som index direkt.

I Azure Machine Learning finns det en [funktionshagemodul](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) som skapar word/phrase-funktioner bekvämt. Följande bild visar ett exempel på hur du använder den här modulen. Indatauppsättningen innehåller två kolumner: bokklassificeringen från 1 till 5 och det faktiska granskningsinnehållet. Målet med den här [funktionshagemodulen](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) är att hämta en massa nya funktioner som visar förekomstfrekvensen för motsvarande ord/fraser i den aktuella bokrecensionen. Så här använder du den här modulen:

* Markera först den kolumn som innehåller indatatexten ("Col2" i det här exemplet).
* För det andra ställer du in "Hashing bitsize" till 8, vilket innebär att 2 ^8=256-funktioner kommer att skapas. Ordet/fasen i all text kommer att hasheras till 256 index. Parametern "Hashing bitsize" varierar från 1 till 31. Ordet/fraserna är mindre benägna att hasheras i samma index om du ställer in det som ett större tal.
* För det tredje ställer du in parametern "N-gram" på 2. Det här värdet hämtar förekomstfrekvensen av unigram (en funktion för varje ord) och bigrams (en funktion för varje par av intilliggande ord) från indatatexten. Parametern "N-gram" varierar från 0 till 10, vilket anger det maximala antalet sekventiella ord som ska ingå i en funktion.  

!["Feature Hashing"-modul](./media/create-features/feature-Hashing1.png)

Följande bild visar hur den här nya funktionen ser ut.

!["Feature Hashing"-exempel](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Slutsats
Konstruerade och valda funktioner ökar effektiviteten i utbildningsprocessen, som försöker extrahera viktig information som finns i data. De förbättrar också kraften i dessa modeller för att klassificera indata korrekt och förutsäga resultat av intresse mer robust. Funktionsteknik och val kan också kombineras för att göra lärandet mer beräkningsmässigt tractable. Det gör det genom att förbättra och sedan minska antalet funktioner som behövs för att kalibrera eller träna en modell. Matematiskt sett är de funktioner som valts för att träna modellen en minimal uppsättning oberoende variabler som förklarar mönstren i data och sedan förutsäga resultaten framgångsrikt.

Det är inte alltid nödvändigtvis att utföra funktionsteknik eller funktionsval. Om det behövs eller inte beror på vilka data som ska lämnas till hands eller samlas in, vilken algoritm som valts och syftet med experimentet.

