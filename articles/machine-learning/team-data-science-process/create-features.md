---
title: Funktionstekniker i datavetenskap - Team Data Science Process
description: Förklarar syftet funktionsframställning och innehåller exempel på dess roll i processen för data förbättring av machine learning.
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
ms.openlocfilehash: 8b0973007a78b492cff1c5ffc2ce1e43116a0847
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835092"
---
# <a name="feature-engineering-in-data-science"></a>Funktionstekniker i datavetenskap
Den här artikeln förklarar syftet funktionsframställning och innehåller exempel på dess roll i processen för data förbättring av machine learning. I exemplen som används för att illustrera denna process hämtas från Azure Machine Learning Studio. 

Den här uppgiften är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Funktionen engineering försöker ökar förutsägande learning-algoritmer genom att skapa funktioner från rådata som underlättar learning processen. Teknik- och urval funktioner är en del av TDSP som beskrivs i den [vad är Team Data Science Process-livscykeln?](overview.md) Funktioner och egenskapsval är delar av den **utveckla funktioner** steg i TDSP. 

* **egenskapsval**: Den här processen försöker att skapa ytterligare relevanta funktioner från de befintliga raw-funktionerna i data och för att öka förutsägande kraften i Inlärningsalgoritmen.
* **Funktionsval**: Den här processen väljer viktiga delmängd av den ursprungliga datafunktioner i ett försök att minska dimensionaliteten för utbildning-problem.

Normalt **funktionstekniker** tillämpas först för att generera ytterligare funktioner, och sedan den **Funktionsval** steg utförs för att ta bort irrelevanta, redundant eller mycket korrelerade funktioner.

Träningsdata som används vid maskininlärning kan ofta förbättras extrahering av funktioner från rådata som samlas in. Ett exempel på en bakåtkompilerade funktion i samband med att lära dig hur du klassificerar bilder av handskriven tecken är skapandet av lite densitet kartan konstrueras från rådata bitars distributionsdata. Den här kartan kan hitta kanter tecknen mer effektivt än att bara använda raw distributionen direkt.

För att skapa funktioner för data i specifika miljöer finns i följande artiklar:

* [Skapa funktioner för data i SQL Server](create-features-sql-server.md)
* [Skapa funktioner för data i ett Hadoop-kluster med hjälp av Hive-frågor](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Skapa funktioner från dina data – funktionstekniker
Träningsdata består av en matris bestående av exempel (poster eller observationer som lagras i rader) som har en uppsättning funktioner (variabler eller fält som lagrats i kolumner). Funktioner som anges i den experimentella designen förväntas beskriva mönster i data. Även om många av fälten rådata kan inkluderas direkt i den valda funktionsuppsättning som används för att träna en modell, men det är ofta fallet att extrafunktioner (bakåtkompilerade) måste konstrueras från funktionerna i rådata för att generera en förbättrad utbildning-datauppsättning.

Vilken typ av funktioner ska skapas för att förbättra datauppsättningen när träna en modell? Bakåtkompilerade funktioner som förbättrar utbildningen ger information som särskiljer bättre mönster i data. De nya funktionerna förväntas innehåller ytterligare information som inte är tydligt avbildade eller enkelt tydligt i den ursprungliga eller en befintlig funktionsuppsättningen. Men den här processen är ungefär en bild. Ljud-och produktiva kräver ofta vissa expertis.

När du börjar med Azure Machine Learning är det enklast att förstå själva processen concretely med hjälp av exemplen i Studio. Två exempel visas här:

* Ett exempel på regression [förutsägelse av antalet uthyrda cyklar](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) i ett övervakat experiment där målvärden är kända
* En text utvinning klassificering exempel med hjälp av [funktions-hashning](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exempel 1: Lägga till den temporala funktioner för en regressionsmodell
Vi använder experiment ”efterfrågeprognostisering av cyklar” i Azure Machine Learning Studio för att visar hur du kan skapa funktioner för en regression aktivitet. Målet med det här experimentet är att förutse behovet av cyklar, det vill säga antalet uthyrda cyklar inom en viss månad/dag/timme. Datauppsättningen ”cykel hyra cykeluthyrningsdata från datauppsättningen” används som indata rådata. Den här datauppsättningen är baserad på verkliga data från kapital Bikeshare företaget som underhåller ett cykel hyra nätverk i Washington DC i USA. Datauppsättningen representerar antalet uthyrda cyklar inom en viss timme per dag under år 2011 och år 2012 och innehåller 17379 rader och 17 kolumner. Rå funktionsuppsättningen innehåller väderförhållanden (temperatur/fuktighet/vindhastighet) och vilken typ av dagen (helgdag/veckodag). Fältet för att förutsäga är ”cnt” antal, som representerar uthyrda cyklar inom en viss timme och vilket mellan 1 och 977.

Med målet att konstruera effektiva funktioner i träningsdata fyra regression modeller skapas med samma algoritm men med datauppsättningar för fyra olika utbildning. De fyra datauppsättningarna representerar samma inkommande rådata, men med ett större antal funktioner. Dessa funktioner är grupperade i fyra kategorier:

1. A = väder + helgdag + veckodag + helgen funktioner för den förväntade dagen
2. B = antal cyklar som har hyrs i var och en av de senaste 12 timmarna
3. C = antal cyklar som har hyrs i var och en av de senaste 12 dagarna i en och samma timme
4. D = antal cyklar som har hyrs i var och en av de föregående 12 veckorna på en och samma timme och samma dag

Förutom funktionen set A, som redan finns i den ursprungliga rådata, skapas de andra tre uppsättningarna med funktioner via funktionen tekniska processen. Funktionsuppsättning B insamlingar de senaste begäran för cyklarna. Funktionen Ange C insamlingar behovet av cyklar på en viss timme. D insamlingar behovet av cyklar funktionsuppsättning på viss timme och viss dag i veckan. De fyra datauppsättningar för utbildning varje innehåller funktionen uppsättning A, A + B, A + B och C och A + B + C + D.

I Azure Machine Learning-experiment bildas dessa fyra datauppsättningar för utbildning via fyra grenar från förbearbetade datauppsättningen för indata. Förutom grenen längst till vänster, var och en av dessa grenar innehåller en [kör R-skript](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modulen, som är härledda funktioner (funktionsuppsättning B och C D) respektive skapas och läggs till de importerade datauppsättningen. Följande bild visar R-skript som används för att skapa funktionsuppsättningen B i andra vänstra grenen.

![Skapa funktioner](./media/create-features/addFeature-Rscripts.png)

En jämförelse av de fyra modellerna prestandaresultat sammanfattas i tabellen nedan: 

![jämförelse av resultat](./media/create-features/result1.png)

Bästa resultat visas för funktionerna A + B + C. Observera att Felfrekvensen minskar när ytterligare funktioner som ingår i träningsdata. Verifierar antagandet att funktionsuppsättningen B, C ger ytterligare relevant information för aktiviteten regression. Men att lägga till funktionen D verkar inte ger någon ytterligare sänkning Felfrekvensen.

## <a name="example2"></a> Exempel 2: Skapa funktioner i text datautvinning
Funktionsframställning används ofta i uppgifter som rör text-utvinning, till exempel dokument klassificering och sentiment analys. Till exempel när du vill klassificera dokument i flera kategorier, är en typisk antagandet att word/fraser som ingår i en kategori för doc mindre sannolikt kan förekomma i en annan doc-kategori. Frekvensen för ord/fraser distribution kan med andra ord att beskriva olika kategorierna. I text-utvinning program, eftersom enskilda delar av text-innehållet är vanligtvis indata, behövs funktionen tekniska processen för att skapa funktioner som involverar ord eller en viss fras frekvenser.

För att uppnå den här uppgiften, en teknik som kallas **funktions-hashning** används för att effektivt förvandla godtyckliga textfunktioner till index. I stället för att koppla varje text-funktionen (ord/fraser) till en viss index, den här metoden fungerar med hjälp av en hash-funktionen till funktioner och direkt med sina hash-värden som index i förväg.

I Azure Machine Learning finns en [funktions-hashning](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul som skapar dessa ord eller en viss fras funktioner smidigt. Följande bild visar ett exempel på hur du använder den här modulen. Den inkommande datauppsättningen innehåller två kolumner: boken omdömet mellan 1 och 5, och det faktiska granska innehållet. Syftet med detta [funktions-hashning](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modulen är att hämta en mängd nya funktioner som visar frekvensen förekomsten av motsvarande ord / fraser som används inom en viss bok granska. Utför följande steg för att använda den här modulen:

* Börja med att välja den kolumn som innehåller indatatexten (”Col2” i det här exemplet).
* Andra värdet ”Hashing bitsize” 8, vilket innebär att 2 ^ 8 = 256-funktioner kommer att skapas. Word/fas i all text ska kodas till 256 index. Parametern ”Hashing bitsize” mellan 1 och 31. Ord / fraser används är mindre troligt att kodas till samma index om att den ska vara ett större antal.
* Det tredje värdet parametern ”N-gram” 2. Det här värdet får förekomsten frekvensen för unigrams (en funktion för varje enstaka ord) och bigrams (en funktion för varje par av intilliggande ord) från den inmatade texten. Parametern ”N-gram” mellan 0 och 10, som anger det maximala antalet sekventiella ord som ska ingå i en funktion.  

![”Funktion Hashing” modul](./media/create-features/feature-Hashing1.png)

Följande bild visar vad de här nya funktionen ser ut.

![”Funktion Hashing” exempel](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Sammanfattning
Bakåtkompilerade och valda funktioner öka effektiviteten för utbildning-processen, som försöker att extrahera den viktiga informationen i data. De kan även förbättra kraften hos dessa modeller att klassificera indata korrekt och att förutsäga resultat av intresse mer kraftigare. Funktioner och egenskapsval kan också kombinera för att göra utbildningsresurser mer beräkningsmässigt tractable. Detta sker genom bättre och minskar antalet funktioner som behövs för att kalibrera eller tränar en modell. De funktioner som valts för att träna modellen är matematiskt sett en minimal uppsättning oberoende variabler som förklarar mönster i data och förutsäga resultat har.

Det är inte alltid nödvändigtvis för val av teknik eller funktion av funktioner. Om det behövs eller inte beror på informationen för hand eller som samlas in, den algoritm som valts och syftet med experimentet.

