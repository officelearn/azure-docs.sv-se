---
title: Felsökning av modul
titleSuffix: Azure Machine Learning service
description: Felsöka modulen undantag i Azure Machine Learning Studio med hjälp av felkoder
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029767"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Undantag och felkoder för algoritm- och modulreferens

Läs mer om felmeddelanden och undantag koder som kan uppstå med moduler i Azure Machine Learning Studio. 

Lös problemet genom att leta efter fel i den här artikeln för att läsa om vanliga orsaker. Det finns två sätt att hämta den fullständiga texten i ett felmeddelande i Studio:  
 
- Klicka på länken **visa utdata logg**i den högra rutan och bläddra till slutet. Det detaljerade felmeddelandet visas i de två sista raderna i fönstret.  
  
- Välj den modul som har felet och klicka på ett rött X. Endast relevant feltexten visas.  
  
Om felmeddelandetext inte är användbart, skicka oss information om kontext och önskad tillägg eller ändringar. Du kan skicka feedback om avsnittet fel eller gå till den [Azure Machine Learning STUDIO-forum](https://aka.ms/aml-forum-studio) och ställa en fråga.  


## <a name="error-0001"></a>Error 0001  
 Undantaget är om en eller flera angivna kolumner i datauppsättning inte gick att hitta.  
  
 Det här felet visas om en Kolumnurval görs för en modul, men den eller de markerade kolumnerna finns inte i den inkommande datauppsättningen. Det här felet kan inträffa om du manuellt anger ett kolumnnamn eller om kolumnväljaren erbjudit en föreslagna kolumn som inte fanns i datauppsättningen när du har kört experimentet.  
  
**Lösning:** Gå tillbaka till den modul som utlöste undantaget och kontrollera att kolumnnamnet eller namnen är korrekta och att alla refererade kolumnerna finns.  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att hitta en eller flera angivna kolumner|  
|Kolumn med namn eller index ”{0}” hittades inte|  
|Kolumn med namn eller index ”{0}” finns inte i ”{1}”|  
 

## <a name="error-0002"></a>Fel 0002  
 Undantaget är om en eller flera parametrar inte kan vara parsade eller konverteras från typen i krävs av metoden måltypen.  
  
 Det här felet uppstår i Azure Machine Learning när du anger en parameter som indata och värdetyp skiljer sig från den typ som förväntas och går inte att utföra implicit konvertering.  
  
**Lösning:** Kontrollera kraven för modulen och avgöra vilken värdetyp krävs (string, heltal, double, osv.)  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att parsa parametern|  
|Det gick inte att parsa ”{0}” parametern|  
|Det gick inte att parsa (konvertera) ”{0}” parametern ”{1}”|  
|Det gick inte att konvertera ”{0}” parametern från ”{1}” till ”{2}”|  
|Det gick inte att konvertera ”{0}” parametervärdet ”{1}” från ”{2}” till ”{3}”|  
|Det gick inte att konvertera värdet ”{0}” i kolumnen ”{1}” från ”{2}” till ”{3}” med användningen av formatet ”{4}” tillhandahålls|  
  

## <a name="error-0003"></a>Fel 0003  
 Undantaget är om en eller flera av indata är null eller tomt.  
  
 Det här felet visas i Azure Machine Learning om några indata eller parametrar till en modul är null eller tomt.  Det här felet kan uppstå, till exempel när du inte skriver ett värde för en parameter. Det kan också inträffa om du väljer en datauppsättning som innehåller värden som saknas eller en tom datauppsättning.  
  
**Lösning:**
 
+ Öppna den modul som producerats undantaget och kontrollera att alla indata har angetts. Se till att alla nödvändiga indata har angetts. 
+ Kontrollera att data som har lästs in från Azure storage kan nås och att kontonamn eller nyckel inte har ändrats.  
+ Kontrollera indata för saknade värden eller null-värden.
+ Om du använder en fråga på en datakälla, kan du kontrollera att data returneras i formatet som du förväntar dig. 
+ Sök efter stavfel eller andra ändringar i data-specifikationen.
  
|Undantag meddelanden|  
|------------------------|  
|En eller flera av indata är null eller tomma|  
|Indata ”{0}” är null eller tomt|  
  

## <a name="error-0004"></a>Error 0004  
 Undantaget är om parametern är mindre än eller lika med specifikt värde.  
  
 Det här felet visas i Azure Machine Learning om parametern i meddelandet är lägre än en gränsvärdet som krävs för att modulen kan bearbeta data.  
  
**Lösning:** Gå tillbaka till den modul som utlöste undantaget och ändra parametern för att vara större än det angivna värdet.  
  
|Undantag meddelanden|  
|------------------------|  
|Parametern måste vara större än gränsvärdet.|  
|Parametern ”{0}” värdet måste vara större än {1}.|  
|Parametern ”{0}” har värdet ”{1}” som ska vara större än {2}|  
  


## <a name="error-0005"></a>Fel 0005  
 Undantaget är om parametern är mindre än ett specifikt värde.  
  
 Du får detta felmeddelande i Azure Machine Learning om parametern i meddelandet är lägre än eller lika med en gränsvärdet som krävs för att modulen kan bearbeta data.  
  
**Lösning:** Gå tillbaka till den modul som utlöste undantaget och ändra parametern för att vara större än eller lika med det angivna värdet.  
  
|Undantag meddelanden|  
|------------------------|  
|Parametern måste vara större än eller lika med gränsvärdet.|  
|Parametern ”{0}” värdet ska vara större än eller lika med {1}.|  
|Parametern ”{0}” har värdet ”{1}” som ska vara större än eller lika med {2}.|  
  

## <a name="error-0006"></a>Fel 0006  
 Undantaget är om parametern är större än eller lika med det angivna värdet.  
  
 Det här felet visas i Azure Machine Learning om parametern i meddelandet är större än eller lika med ett värde för gräns som krävs för att modulen kan bearbeta data.  
  
**Lösning:** Gå tillbaka till den modul som utlöste undantaget och ändra parametern ska vara mindre än det angivna värdet.  
  
|Undantag meddelanden|  
|------------------------|  
|Matchningsfel för parametrar. En av parametrarna ska vara mindre än en annan.|  
|Parametern ”{0}” värde får innehålla högst parametern ”{1}” värde.|  
|Parametern ”{0}” har värdet ”{1}” som ska vara mindre än {2}.|  
  

## <a name="error-0007"></a>Fel 0007  
 Undantaget är om parametern är större än ett specifikt värde.  
  
 Det här felet visas i Azure Machine Learning om du har angett ett värde som är större än vad som tillåts i egenskaperna för modulen. Exempelvis kan du ange data som är utanför intervallet för stöds datum eller du kan tyda på att fem kolumner ska användas när bara tre kolumner är tillgängliga. 
 
 Du kan också se det här felet om du anger två uppsättningar av data som måste matcha på något sätt. Om du byter namn på kolumner och ange vilka kolumner som efter index, måste antalet namn som du anger matchar antalet kolumnindex. Ett annat exempel kanske en matematiska åtgärden som använder två kolumner där kolumnerna måste ha samma antal rader. 
  
**Lösning:**
 
 + Öppna modulen i fråga och granska alla inställningar för numeriska egenskap.
 + Se till att alla parametervärden faller inom intervallet med värden som stöds för egenskapen.
 + Om modulen tar flera inmatningar kan du kontrollera att indata är av samma storlek.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Kontrollera om datauppsättningen eller datakälla har ändrats. Ett värde som fungerade med en tidigare version av data misslyckas ibland när antalet kolumner, datatyperna för kolumnen eller storleken på data har ändrats.  
  
|Undantag meddelanden|  
|------------------------|  
|Matchningsfel för parametrar. En av parametrarna bör vara mindre än eller lika med till en annan.|  
|Parametern ”{0}” värdet ska mindre än eller lika med parametern ”{1}” värde.|  
|Parametern ”{0}” har värdet ”{1}” som ska vara mindre än eller lika med {2}.|  
  

## <a name="error-0008"></a>Fel 0008  
 Undantaget är om parametern inte är i intervallet.  
  
 Det här felet visas i Azure Machine Learning om parametern i meddelandet är utanför det giltiga intervallet som krävs för att modulen kan bearbeta data.  
  
 Till exempel det här felet visas om du försöker använda [Lägg till rader](add-rows.md) att kombinera två datauppsättningar som har olika antal kolumner.  
  
**Lösning:** Gå tillbaka till den modul som utlöste undantaget och ändra parametern för att ligga inom det angivna intervallet.  
  
|Undantag meddelanden|  
|------------------------|  
|Parametervärdet är inte i det angivna intervallet.|  
|Parametern ”{0}” värdet ligger inte inom intervallet.|  
|Parametern ”{0}” värdet ska vara i intervallet [{1}, {2}].|  
  

## <a name="error-0009"></a>Fel 0009  
 Undantaget är när Azure storage-kontonamn eller behållarnamn har angetts felaktigt.  
  
Det här felet inträffar i Azure Machine Learning Studio när du ange parametrar för ett Azure storage-konto, men namn eller lösenord kan inte matchas. På lösenord eller kontonamn kan inträffa av flera orsaker:
 
 + Kontot har fel typ. Vissa nya kontotyper stöds inte för användning med Machine Learning Studio. Se [importdata](import-data.md) mer information.
 + Du har angett felaktig kontonamnet
 + Kontot finns inte längre
 + Lösenordet för lagringskontot är felaktig eller har ändrats
 + Du angav inte behållarens namn eller behållaren finns inte
 + Du angav inte helt sökvägen till filen (sökväg till bloben)
   
**Lösning:**

Dessa problem uppstår ofta vid försök att manuellt ange namn, lösenord eller behållare sökvägen till kontot. Vi rekommenderar att du använder guiden Nytt för den [importdata](import-data.md) modulen, som hjälper dig att leta upp och kontrollera namn.

Kontrollera även om det konto, behållare eller blob har tagits bort. Använd ett annat Azure storage-verktyg för att kontrollera att kontonamnet och lösenordet har angetts på rätt sätt och att behållaren finns. 

Vissa nyare kontotyper stöds inte av Azure Machine Learning. Till exempel den nya ”frekvent” eller ”kalla” lagringstyper kan inte användas för maskininlärning. Både klassiska lagringskonton och storage-konton som skapats som ”allmänt” fungerar.

Om den fullständiga sökvägen till en blob har angetts, kontrollerar du att sökvägen har angetts som **behållare/blobname**, och att både behållaren och blob finns i kontot.  
  
 Sökvägen får inte innehålla ett inledande snedstreck. Till exempel **/behållare/blob** är felaktig och ska anges som **behållare/blob**.  

  
|Undantag meddelanden|  
|------------------------|  
|Azure storage-kontonamn eller behållarens namn är felaktigt.|  
|Namnet på Azure storage-kontot ”{0}” eller behållarnamn ”{1}” är felaktigt; ett behållarnamn på formatet behållare/blob förväntades.|  
  

## <a name="error-0010"></a>Fel 0010  
 Undantaget är om indatauppsättningar ha kolumnnamn som ska matcha inte men.  
  
 Det här felet visas i Azure Machine Learning Om kolumnindex i meddelandet har andra kolumnnamn i de två datauppsättningarna som indata.  
  
**Lösning:** Använd [redigera Metadata](edit-metadata.md) eller ändra den ursprungliga datauppsättningen så att den har samma kolumnnamn för det angivna kolumnindexet.  
  
|Undantag meddelanden|  
|------------------------|  
|Kolumner med motsvarande index i indatauppsättningar har olika namn.|  
|Kolumnnamn är inte desamma för kolumnen {0} (nollbaserat) för indatauppsättningar ({1} och {2} respektive).|  
  

## <a name="error-0011"></a>Fel 0011  
 Undantaget är om du kolumnen set argumentet inte gäller för någon av kolumner för datauppsättningar.  
  
 Det här felet visas i Azure Machine Learning om den angivna Kolumnurval inte matchar någon av kolumnerna i den givna datamängden.  
  
 Du kan också få det här felet om du inte har markerat en kolumn och det krävs minst en kolumn för modulen ska fungera.  
  
**Lösning:** Ändra Kolumnurval i modulen så att den gäller för kolumner i datauppsättningen.  
  
 Om modulen kräver att du väljer en viss kolumn, till exempel en etikett-kolumn, kontrollerar du att den högra kolumnen har valts.  
  
 Om olämpliga kolumner har valts, ta bort dem och köra om experimentet.  
  
|Undantag meddelanden|  
|------------------------|  
|Angivna kolumnuppsättning gäller inte för någon av kolumner för datauppsättningar.|  
|Angiven kolumn ”{0}” gäller inte för någon av kolumner för datauppsättningar.|  
  

## <a name="error-0012"></a>Fel 0012  
 Undantaget är om instans av klassen inte kunde skapas med skickade uppsättning argument.  
  
**Lösning:** Det här felet kan inte åtgärdas av användaren och kommer att inaktualiseras i en framtida version.  
  
|Undantag meddelanden|  
|------------------------|  
|Omdöme modell, öva modellen först.|  
|Omdöme modell ({0}), Använd tränade modellen.|  
  

## <a name="error-0013"></a>Fel 0013  
 Undantaget är om learner som skickats till modulen är en ogiltig typ.  
  
 Det här felet inträffar när en tränad modell är inte kompatibel med anslutna poängsättningsmodul. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Lösning:**

Bestämmer vilken typ av learner som produceras av modulen utbildning och bestäm poängsättningsmodul som är lämplig för eleven. 

Om modellen har lärt sig med hjälp av någon av modulerna som specialiserad utbildning, den tränade modellen bara ansluta till motsvarande specialiserade poängsättningsmodul. 


|Modelltypen|Modul för utbildning| Poängsättningsmodul|
|----|----|----|
|alla klassificerare|[Träna modell](train-model.md) |[Poängsätta modell](score-model.md)|
|alla regression-modellen|[Träna modell](train-model.md) |[Poängsätta modell](score-model.md)|
<!--| klustring modeller| [Träna klustermodellen](train-clustering-model.md) eller [klustring för Svepvinkeln](sweep-clustering.md)| [Tilldela Data till ett kluster](assign-data-to-clusters.md)|
| avvikelseidentifiering – en klass SVM | [Träna modell för identifiering av avvikelser](train-anomaly-detection-model.md) |[Poängsätta modell](score-model.md)|
| avvikelseidentifiering - PCA |[Träna modell](train-model.md) |[Poängsätta modell](score-model.md) </br> Några ytterligare steg krävs för att utvärdera modellen. |
| avvikelseidentifiering - tidsserier|  [Time Series-Avvikelseidentifiering](time-series-anomaly-detection.md) |Modellen träna från data och genererar resultat. Modulen skapar inte en tränad learner och ingen ytterligare bedömning krävs. |
| rekommendationen modell| [Träna Matchbox rekommenderare](train-matchbox-recommender.md) | [Poäng Matchbox rekommenderare](score-matchbox-recommender.md) |
| bildklassificering | [Tränats Cascade Bildklassificering](pretrained-cascade-image-classification.md) | [Poängsätta modell](score-model.md) |
|Vowpal Wabbit modeller| [Träna Vowpal Wabbit Version 7-4-modell](train-vowpal-wabbit-version-7-4-model.md) | [Bedömningsmodell Vowpal Wabbit Version 7 – 4](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit modeller| [Träna Vowpal Wabbit Version 7 – 10 modell](train-vowpal-wabbit-version-7-10-model.md) | [Bedömningsmodell Vowpal Wabbit Version 7 – 10](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit modeller| [Öva modell som Vowpal Wabbit Version 8](score-vowpal-wabbit-version-8-model.md) | [Poängmodell Vowpal Wabbit Version 8](score-vowpal-wabbit-version-8-model.md) |-->
  
|Undantag meddelanden|  
|------------------------|  
|Learner av en ogiltig typ skickas.|  
|Learner ”{0}” har den ogiltiga typen.|  


## <a name="error-0014"></a>Fel 0014  
 Undantaget är om antalet unika värden i kolumnen är större än tillåtet.  
  
 Det här felet uppstår när en kolumn innehåller för många unika värden.  Till exempel kanske du ser detta fel om du anger att en kolumn ska hanteras som kategoriska data, men det finns för många unika värden i kolumnen för att tillåta bearbetning att slutföra. Du kan också se det här felet om det finns ett matchningsfel mellan antalet unika värden i två indatavärdena.   
  
**Lösning:**

Öppna den modul som gav upphov till felet och identifiera de kolumner som används som indata. För vissa moduler, högerklickar du på datauppsättningen för indata och välj **visualisera** att hämta statistik på enskilda kolumner, inklusive antalet unika värden och deras distribution.

Vidta åtgärder för att minska antalet unika värden i kolumnerna för kolumner som du vill använda för gruppering eller kategorisering. Du kan minska på olika sätt, beroende på datatypen för kolumnen. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Det går inte att hitta en lösning som passar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på den modul som genererade felet, och datatyp och på kolumnens kardinalitet. Vi använder informationen om för att tillhandahålla mer riktad felsökningssteg för vanliga scenarier.   
  
|Undantag meddelanden|  
|------------------------|  
|Antalet unika värden i kolumnen är större än tillåtet.|  
|Antalet unika värden i kolumnen ”:{0}” överskrider tuppel antal {1}.|  
  

## <a name="error-0015"></a>Fel 0015  
 Undantaget är om databasanslutningen misslyckades.  
  
 Du får det här felet om du anger en felaktig SQL-kontonamn, lösenord, databasservern eller databasnamn, eller om det inte går att upprätta en anslutning med databasen på grund av problem med databas- eller.  
  
**Lösning:** Kontrollera att kontonamnet, lösenord, database-server och databas har angetts på rätt sätt och att det angivna kontot har rätt behörighetsnivå. Kontrollera att databasen är för närvarande tillgänglig.  
  
|Undantag meddelanden|  
|------------------------|  
|Fel vid databasanslutning.|  
|Fel vid databasanslutning: {0}.|  
  


## <a name="error-0016"></a>Fel 0016  
 Undantaget är om indatauppsättningar som skickats till modulen bör har kompatibla kolumntyper inte men.  
  
 Det här felet visas i Azure Machine Learning om vilka typer av kolumner som skickades i två eller flera datauppsättningar inte är kompatibla med varandra.  
  
**Lösning:** Använd [redigera Metadata](edit-metadata.md) eller ändra den ursprungliga datauppsättningen för indata<!--, or use [Convert to Dataset](convert-to-dataset.md)--> Se till att typerna av kolumnerna som är kompatibla.  
  
|Undantag meddelanden|  
|------------------------|  
|Kolumner med motsvarande index i indatauppsättningar har inkompatibla typer.|  
|Kolumner {0} och {1} är inkompatibla.|  
|Kolumntyper för elementet är inte kompatibla för kolumnen {0} (nollbaserat) för indatauppsättningar ({1} och {2} respektive).|  
  

## <a name="error-0017"></a>Fel 0017  
 Undantaget är om en vald kolumn använder en datatyp som inte stöds av den aktuella modulen.  
  
 Exempelvis kan kanske du får detta felmeddelande i Azure Machine Learning om valet av kolumnen innehåller en kolumn med en datatyp som inte kan bearbetas av modulen, till exempel en strängkolumn för en matematiska åtgärden eller en poäng-kolumn där en kolumn för kategoriska funktion är krävs.  
  
**Lösning:**
 1. Identifiera den kolumn som är problemet.
 2. Granska kraven för modulen.
 3. Ändra kolumnen så att den uppfyller kraven. Du kan behöva använda flera av följande moduler för att göra ändringar, beroende på kolumnen och konverteringen du:
    + Använd [redigera Metadata](edit-metadata.md) att ändra datatypen för kolumnerna eller ändra kolumnen användning från funktionen till numeriska, kategoriska till icke-kategoriska och så vidare.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Som en sista utväg, kan du behöva ändra den ursprungliga datauppsättningen för indata.

> [!TIP]
> Det går inte att hitta en lösning som passar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på den modul som genererade felet, och datatyp och på kolumnens kardinalitet. Vi använder informationen om för att tillhandahålla mer riktad felsökningssteg för vanliga scenarier. 
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att bearbeta kolumn av aktuella typen. Typen stöds inte av modulen.|  
|Det går inte att bearbeta kolumn av typen {0}. Typen stöds inte av modulen.|  
|Det går inte att bearbeta kolumnen ”{1}” av typen {0}. Typen stöds inte av modulen.|  
|Det går inte att bearbeta kolumnen ”{1}” av typen {0}. Typen stöds inte av modulen. Parameternamn: {2}|  
  

## <a name="error-0018"></a>Fel 0018  
 Undantaget är om datauppsättningen för indata inte är giltig.  
  
**Lösning:** Det här felet i Azure Machine Learning kan visas i många kontexter, så det är inte en enda lösning. I allmänhet indikerar felet att data anges som indata till en modul har fel antal kolumner, eller att datatypen inte matchar kraven i modulen. Exempel:  
  
-   Modulen kräver en etikett-kolumn, men ingen kolumn har markerats som en etikett eller du har inte valt en etikett kolumn ännu.  
  
-   Modulen kräver att data vara kategoriska men dina data är numeriskt.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Data är i fel format.  
  
-   Importerade data innehåller ogiltiga tecken, felaktiga värden eller utanför intervallet.  
-   Kolumnen är tom eller innehåller för många värden som saknas.  
  
 För att fastställa kraven och hur dina data kan du granska hjälpavsnittet för den modul som kommer att konsumera datauppsättningen som indata.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Undantag meddelanden|  
|------------------------|  
|Datauppsättningen är inte giltig.|  
|{0} innehåller ogiltiga data.|  
|{0} och {1} ska vara konsekvent kolumnen klokt.|  
  

## <a name="error-0019"></a>Error 0019  
 Undantaget är om kolumnen förväntas innehålla sorterade värden, men det inte.  
  
 Det här felet visas i Azure Machine Learning om angivna kolumnvärdena inte fungerar.  
  
**Lösning:** Sortera kolumnvärdena genom att manuellt ändra den inkommande datauppsättningen och kör modulen.  
  
|Undantag meddelanden|  
|------------------------|  
|Värdena i kolumnen sorteras inte.|  
|Värdena i kolumnen ”{0}” sorteras inte.|  
|Värdena i kolumnen ”{0}” i datauppsättning ”{1}” sorteras inte.|  
  

## <a name="error-0020"></a>Fel 0020  
 Undantaget är om antalet kolumner i några av de datauppsättningar som skickats till modulen är för liten.  
  
 Du får det här felet i Azure Machine Learning om det inte finns tillräckligt med kolumner har valts för en modul.  
  
**Lösning:** Gå tillbaka till modulen och se till att kolumnväljaren har rätt antal kolumner har valts.  
  
|Undantag meddelanden|  
|------------------------|  
|Antalet kolumner i datauppsättningen för indata är mindre än det tillåtna minimivärdet.|  
|Antalet kolumner i datauppsättningen för indata är mindre än det tillåtna minimivärdet för {0} kolumn(er).|  
|Antalet kolumner i datauppsättningen för indata ”{0}” är mindre än det tillåtna minimivärdet {1} kolumn(er).|

## <a name="error-0021"></a>Fel 0021  
 Undantaget är om antalet rader i några av de datauppsättningar som skickats till modulen är för liten.  
  
 Det här felet i visas i Azure Machine Learning när det finns inte tillräckligt med rader i datauppsättningen för att utföra den angivna åtgärden. Du kan till exempel se det här felet om den inkommande datauppsättningen är tom, eller om du försöker utföra en åtgärd som kräver vissa minsta antal rader som ska vara giltigt. Sådana åtgärder kan inkludera (men är inte begränsat till) gruppering eller klassificering utifrån statistiska metoder, vissa typer av diskretisering och inlärning med antal.  
  
**Lösning:**
 
 + Öppna den modul som returnerade fel och kontrollera egenskaperna för inkommande datamängd och modulen. 
 + Kontrollera att datauppsättningen för indata inte är tom och att det inte finns tillräckligt med rader med data för att uppfylla kraven som beskrivs i hjälpen för modulen.  
 + Om dina data har lästs in från en extern källa, kontrollera att datakällan är tillgänglig och att det finns inga fel eller ändra i datadefinitionen som skulle orsaka importen att få färre rader.
 + Om du utför en åtgärd på data uppströms av modulen som kan påverka vilken typ av data eller antalet värden, till exempel rensning, dela, eller ansluta till operations Kontrollera utdata för dessa åtgärder och avgör hur många rader som returneras.  



## <a name="error-0022"></a>Fel 0022  
 Undantaget är om antalet valda kolumner i datauppsättningen för indata inte är lika med det förväntade antalet.  
  
 Det här felet i Azure Machine Learning kan inträffa när den underordnade modulen eller åtgärden kräver ett visst antal kolumner eller indata, och du har angett för få eller för många kolumner eller indata. Exempel:  
  
-   Du anger en enskild etikett kolumn eller en nyckelkolumn och av misstag valde flera kolumner.  
  
-   Du byter namn på kolumner, men angetts namn på fler eller färre än det finns kolumner.  
  
-   Antalet kolumner i käll- och målservrar har ändrats eller matchar inte antalet kolumner som används av modulen.  
  
-   Du har angett en kommaavgränsad lista med värden för indata, men matchar inte antalet värden eller flera inmatningar stöds inte.  
  
**Lösning:** Gå tillbaka till modulen och kontrollera Kolumnurval för att säkerställa att rätt antal kolumner har valts. Kontrollera utdata för överordnade moduler och kraven för underordnade åtgärder.  
  
 Om du använde något av alternativ för val av kolumn som kan välja flera kolumner (kolumnindex, alla funktioner, siffror, o.s.v.), verifierar du det exakta antalet kolumner som returneras av valet.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Kontrollera att talet eller typ av överordnad kolumner inte har ändrats.  
  
 Om du använder en rekommendation datauppsättning för att träna en modell, Kom ihåg att rekommenderare förväntar sig ett begränsat antal kolumner, motsvarar användare-item par eller user-objekt – rangordning. Ta bort ytterligare kolumner innan du träna modellen eller dela upp rekommendationen datauppsättningar. Mer information finns i [dela Data](split-data.md).  
  
|Undantag meddelanden|  
|------------------------|  
|Antalet valda kolumner i datauppsättningen för indata är inte lika med det förväntade antalet.|  
|Antalet valda kolumner i datauppsättningen för indata är inte lika {0}.|  
|Mönster för val av kolumnen ”{0}” innehåller antalet valda kolumner i datauppsättningen för indata inte lika med {1}.|  
|Mönster för val av kolumnen ”{0}” förväntas ge {1} kolumnerna som valts i datauppsättningen för indata, men {2} kolumnerna är/tillhandahålls.|  



## <a name="error-0023"></a>Fel 0023  
 Undantaget är om målkolumnen av datauppsättningen för indata inte är giltigt för den aktuella trainer-modulen.  
  
 Det här felet i Azure Machine Learning uppstår om målkolumnen (som markerats i modulparametrar) är inte giltig datatyp, ingår alla värden som saknas eller är inte kategoriska som förväntat.  
  
**Lösning:** Gå tillbaka till modulen är indata för att granska innehållet i etiketten/målkolumnen. Kontrollera att den inte har alla värden som saknas. Om modulen är förväntades målkolumnen vara kategoriska, se till att det finns fler än en distinkta värden i målkolumnen.  
  
|Undantag meddelanden|  
|------------------------|  
|Datauppsättningen för indata har som inte stöds målkolumnen.|  
|Datauppsättningen för indata har stöds inte target kolumnen ”{0}”.|  
|Datauppsättningen för indata har stöds inte target kolumnen ”{0}” för learner av typen {1}.|  
 

## <a name="error-0024"></a>Error 0024  
Undantaget är om datauppsättning inte innehåller någon etikett-kolumn.  

 Det här felet i Azure Machine Learning inträffar när modulen kräver en etikett-kolumn och datauppsättningen inte har en etikett-kolumn. Utvärdering av en poängsatta datauppsättning kräver till exempel vanligtvis att det finns en etikett kolumn att beräkna mått för Precision.  
 
Det kan också hända att en etikett-kolumn i datauppsättningen, men inte identifieras korrekt av Azure Machine Learning.
  
**Lösning:**

+ Öppna den modul som gav upphov till felet och avgöra om det finns en etikett-kolumn. Namn eller data typen för kolumnen spelar så länge som kolumnen innehåller en enda resultat (eller beroende variabel) som du försöker att förutsäga. Om du inte är säker på vilken kolumn har etiketten leta efter ett allmänt namn som *klass* eller *Target*. 
+  Om datauppsättningen inte innehåller en kolumn för etikett, är det möjligt att etikettkolumnen har uttryckligen eller av misstag tagits bort uppströms. Det kan också vara att datauppsättningen inte är utdata från en överordnad poängsättningsmodul.
+ Om du vill uttryckligen Markera kolumnen som etikettkolumnen, lägger du till den [redigera Metadata](edit-metadata.md) modulen och ansluta datauppsättningen. Välj bara etikettkolumnen och välj **etikett** från den **fält** listrutan. 
+ Om du väljer fel kolumn som etiketten du kan välja **Rensa etikett** från den **fält** åtgärda metadata för kolumnen. 
  
|Undantag meddelanden|  
|------------------------|  
|Det finns ingen etikett-kolumn i datauppsättningen.|  
|Det finns ingen etikett-kolumn i ”{0}”.|  
  

## <a name="error-0025"></a>Fel 0025  
 Undantaget är om datauppsättning inte innehåller en resultatkolumnen.  
  
 Det här felet i Azure Machine Learning uppstår om indata för att utvärdera modellen inte innehåller giltig poäng kolumner. Exempelvis kan försöker användaren utvärdera en datauppsättning innan den har poängsätts med en korrekt träningsmodell eller resultatkolumnen uppströms uttryckligen togs bort. Det här undantaget inträffar även om kolumnerna poäng för de två datauppsättningar är inkompatibla. Du kan exempelvis ha försökt att jämföra det arbete du utfört en linjär regressor med som en binär klassificerare.  
  
**Lösning:** Gå tillbaka till indata för att utvärdera modellen och Undersök om det innehåller en eller flera kolumner i resultatet. Om inte, datauppsättningen poängsätts inte eller poäng kolumner har tagits bort i en överordnad modul.  
  
|Undantag meddelanden|  
|------------------------|  
|Det finns ingen poäng-kolumn i datauppsättningen.|  
|Det finns ingen poäng kolumn i ”{0}”.|  
|Det finns ingen poäng kolumn i ”{0}” som produceras av en ”{1}”. Bedöma datauppsättningen med rätt typ av learner.|  
  

## <a name="error-0026"></a>Fel 0026  
 Undantaget är om kolumner med samma namn inte är tillåtna.  
  
 Det här felet i Azure Machine Learning uppstår om flera kolumner har samma namn. Ett sätt som du kan få detta felmeddelande är om datauppsättningen har inte en rubrikrad och kolumnnamn tilldelas automatiskt: Col0, Kol1 osv.  
  
**Lösning:** Om kolumner har samma namn, infoga en [redigera Metadata](edit-metadata.md) modulen datauppsättningen för indata och modulen. Använda kolumnväljaren i [redigera Metadata](edit-metadata.md) att välja kolumner som ska byta namn, att skriva de nya namnen till de **nya kolumnnamnen** textrutan.  
  
|Undantag meddelanden|  
|------------------------|  
|Lika med kolumnnamn har angetts i argumenten. Lika med kolumnnamn tillåts inte av modulen.|  
|Kolumnnamnen i argument som är lika med ”{0}” och ”{1}” är inte tillåtna. Ange olika namn.|  
  

## <a name="error-0027"></a>Fel 0027  
 Undantaget är om när två objekt måste vara av samma storlek men finns inte.  
  
 Detta är ett vanligt fel i Azure Machine Learning och kan orsakas av många villkor.  
  
**Lösning:** Det finns ingen specifik lösning. Du kan dock söka efter villkor som följande:  
  
-   Om du byter namn på kolumner, se till att varje lista (indatakolumnerna och listan över nya namn) har samma antal objekt.  
  
-   Om du ansluter till eller sammanfoga två datauppsättningar, kontrollera att de har samma schema.  
  
-   Om du ansluter till två datauppsättningar som har flera kolumner, se till att nyckelkolumnerna har samma datatyp, och väljer alternativet **tillåter dubbletter och bevara kolumnordning i val av**.  
  
|Undantag meddelanden|  
|------------------------|  
|Storleken på skickade objekt är inkonsekvent.|  
|Storleken på ”{0}” stämmer inte med storleken på ”{1}”.|  
  

## <a name="error-0028"></a>Fel 0028  
 Undantaget i fall när kolumnuppsättning som innehåller duplicerade kolumnnamn och det är inte tillåtet.  
  
 Det här felet i Azure Machine Learning uppstår när kolumnnamn dupliceras; det vill säga inte unikt.  
  
**Lösning:** Om några kolumner har samma namn, Lägg till en instans av [redigera Metadata](edit-metadata.md) datauppsättningen för indata och modulen höja felet. Använda kolumnväljaren i [redigera Metadata](edit-metadata.md) att välja kolumner som ska byta namn på och skriver de nya kolumnnamnen i den **nya kolumnnamnen** textrutan. Om du byter namn på flera kolumner, kontrollera att de värden du anger i den **nya kolumnnamnen** är unika.  
  
|Undantag meddelanden|  
|------------------------|  
|Kolumnuppsättning innehåller duplicerade kolumnen namn.|  
|Namnet ”{0}” är en dubblett.|  
|Namnet ”{0}” är en dubblett i ”{1}”.|  
  

## <a name="error-0029"></a>Fel 0029  
 Undantaget är om när ogiltig URI skickas.  
  
 Det här felet i Azure Machine Learning uppstår om när ogiltig URI skickas.  Du får det här felet om något av följande villkor är uppfyllt:, eller.  
  
-   Offentlig eller SAS-URI har angetts för Azure Blob Storage för läsning eller skrivning innehåller ett fel.  
  
-   Tidsfönster tills Signaturen har upphört att gälla.  
  
-   Webbtjänstens URL via HTTP-källan representerar en fil eller en loopback-URI.  
  
-   Webbtjänstens URL via HTTP innehåller ett felaktigt formaterat URL.  
  
-   URL: en kan inte matchas av remote källa.  
  
**Lösning:** Gå tillbaka till modulen och kontrollera formatet på URI: N. Om datakällan är en URL för via HTTP, kontrollerar du att den avsedda källan inte är en fil eller en loopback-URI (localhost).  
  
|Undantag meddelanden|  
|------------------------|  
|Ogiltig Uri skickas.|  
  

## <a name="error-0030"></a>Fel 0030  
 Undantaget i fall när det inte går att hämta en fil.  
  
 Det här undantaget i Azure Machine Learning inträffar när det inte går att hämta en fil. Du får det här undantaget när ett försök har gjorts från en HTTP-källa har gick inte att läsa tre (3) försök igen försöker.  
  
**Lösning:** Kontrollera att URI: N till den HTTP-källan är korrekt och att webbplatsen är för närvarande tillgänglig via Internet.  
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att hämta en fil.|  
|Fel vid nedladdning av filen: {0}.|  
  

## <a name="error-0031"></a>Fel 0031  
 Undantaget är om antalet kolumner i kolumnuppsättningen är mindre än vad som behövs.  
  
 Det här felet i Azure Machine Learning uppstår om antalet kolumner som valts är mindre än vad som behövs.  Du får det här felet om det minsta antalet kolumner som inte är markerade.  
  
**Lösning:** Lägg till fler kolumner i kolumnen med den **kolumnväljaren**.  
  
|Undantag meddelanden|  
|------------------------|  
|Antalet kolumner i kolumnuppsättningen är mindre än vad som krävs.|  
|{0} kolumnerna ska anges. Det faktiska antalet angivna kolumnerna är {1}.|  

## <a name="error-0032"></a>Fel 0032  
 Undantaget är om argumentet inte är ett tal.  
  
 Det här felet visas i Azure Machine Learning om argumentet är ett double-värde eller NaN.  
  
**Lösning:** Ändra det angivna argumentet för att använda ett giltigt värde.  
  
|Undantag meddelanden|  
|------------------------|  
|Argumentet är inte ett tal.|  
|”{0}” är inte ett tal.|  
  

## <a name="error-0033"></a>Fel 0033  
 Undantaget är om argumentet är oändlighet.  
  
 Det här felet i Azure Machine Learning uppstår om argumentet är oändliga. Du får det här felet om argumentet är antingen `double.NegativeInfinity` eller `double.PositiveInfinity`.  
  
**Lösning:** Ändra det angivna argumentet för att vara ett giltigt värde.  
  
|Undantag meddelanden|  
|------------------------|  
|Argumentet måste vara begränsad.|  
|”{0}” är inte begränsad.|  
  

## <a name="error-0034"></a>Fel 0034  
 Undantaget är om mer än en klassificering finns för ett par user-objektet.  
  
 Det här felet i Azure Machine Learning uppstår i rekommendationen om ett par user-objektet har mer än en klassificering värde.  
  
**Lösning:** Se till att användaren-item paret har endast ett omdöme-värde.  
  
|Undantag meddelanden|  
|------------------------|  
|Det finns fler än en klassificering för värdena i datauppsättningen.|  
|Mer än en klassificering för användaren {0} och objekt {1} i datatabell för klassificering förutsägelse.|  
  

## <a name="error-0035"></a>Fel 0035  
 Undantaget är om inga funktioner har angetts för en viss användare eller objekt.  
  
 Det här felet i Azure Machine Learning inträffar du försöker använda en rekommendation modell för bedömning men går inte att hitta en funktion-vektor.  
  
**Lösning:**

Matchbox rekommenderare har vissa krav som måste uppfyllas när du använder objektet funktioner eller funktioner.  Det här felet indikerar att en funktion vector saknas för en användare eller objekt som du angav som indata.  Du måste se till att det finns en vektor med funktioner i data för varje användare eller objekt.  
  
 Till exempel om du har tränats en rekommendation modell med funktioner som användarens ålder, plats eller intäkter, men nu vill du skapa poäng för nya användare som inte visades vid träning, måste du ange vissa motsvarande uppsättning funktioner (dvs, ålder, plats, och intäkter värden) för nya användare för att göra lämpliga förutsägelser för dessa. 
 
 Om du inte har några funktioner för dessa användare kan du funktionsframställning att generera lämpliga funktioner.  Om du inte har värden för enskilda användare ålder eller intäkter, kan du generera ungefärliga värden som ska användas för en grupp användare. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Inte tillämpligt för ditt ärende lösning? Är du Välkommen att skicka feedback på den här artikeln och ange information om scenariot med modulen och antalet rader i kolumnen. Vi använder den här informationen för att tillhandahålla mer detaljerade felsökningsanvisningar i framtiden.
   
|Undantag meddelanden|  
|------------------------|  
|Inga funktioner har angetts för en begäran om att användaren eller objekt.|  
|Funktioner för {0} krävs men har inte angetts.|  
  

## <a name="error-0036"></a>Fel 0036  
 Undantaget är om flera funktionen vektorer har angetts för en viss användare eller objekt.  
  
 Det här felet i Azure Machine Learning inträffar om en funktion-vektor har definierats mer än en gång.  
  
**Lösning:** Kontrollera att funktionen-vektor inte har definierats mer än en gång.  
  
|Undantag meddelanden|  
|------------------------|  
|Duplicera funktionsdefinitionen för en användare eller objekt.|  
|Duplicera funktionsdefinitionen för {0}.|  
  

## <a name="error-0037"></a>Fel 0037  
 Undantaget är om flera etikettkolumner har angetts och bara en tillåts.  
  
 Det här felet i Azure Machine Learning uppstår om mer än en kolumn har markerats att vara den nya etikettkolumnen. Mest övervakat learning-algoritmer kräver en kolumn har markerats som mål eller etikett.  
  
**Lösning:** Se till att välja en enda kolumn som den nya etikettkolumnen.  
  
|Undantag meddelanden|  
|------------------------|  
|Flera etikettkolumner har angetts.|  
  

## <a name="error-0038"></a>Fel 0038  
 Undantag uppstår om antalet förväntade element ska vara ett exakt värde, men är inte.  
  
 Det här felet i Azure Machine Learning uppstår om antalet förväntade element ska vara ett exakt värde, men är inte.  Du får det här felet om antalet element inte är lika med giltig förväntat värde.  
  
**Lösning:** Ändra indata för att ha rätt antal element.  
  
|Undantag meddelanden|  
|------------------------|  
|Antalet element är inte giltig.|  
|Antalet element i ”{0}” är inte giltig.|  
|Antalet element i ”{0}” är inte lika med giltigt antal {1} element.|  
  

## <a name="error-0039"></a>Fel 0039  
 Undantaget är om en åtgärd har misslyckats.  
  
 Det här felet i Azure Machine Learning inträffar när en intern åtgärd inte kan slutföras.  
  
**Lösning:** Det här felet beror på många villkor och det inte finns någon specifik åtgärd.  
 I följande tabell innehåller allmänna meddelanden för det här felet, som följs av en specifik beskrivning av villkoret. 
 
 Om det finns ingen information om, [skicka feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) och ger information om de moduler som genererade fel och relaterade villkor.
  
|Undantag meddelanden|  
|------------------------|  
|Åtgärden kunde inte utföras.|  
|Fel uppstod när du har slutfört åtgärden: {0}.|  
  

## <a name="error-0040"></a>Error 0040  
 Undantaget är när du anropar en föråldrad modul.  
  
 Det här felet i Azure Machine Learning skapas när du anropar en föråldrad modul.  
  
**Lösning:** Ersätt föråldrad modulen med stöds. Se modul utdataloggen för information om vilken modul ska användas i stället.  
  
|Undantag meddelanden|  
|------------------------|  
|Åtkomst till föråldrad modulen.|  
|Modulen ”{0}” är inaktuell. Använd modulen ”{1}” i stället.|  
 

## <a name="error-0041"></a>Fel 0041  
 Undantaget är när du anropar en föråldrad modul.  
  
 Det här felet i Azure Machine Learning skapas när du anropar en föråldrad modul.  
  
**Lösning:** Ersätt föråldrad modulen med en uppsättning av stöds. Den här informationen ska vara synliga i modulen utdataloggen.  
  
|Undantag meddelanden|  
|------------------------|  
|Åtkomst till föråldrad modulen.|  
|Modulen ”{0}” är inaktuell. Använda modulerna ”{1}” för begärda funktioner.|  
 

## <a name="error-0042"></a>Error 0042  
 Undantaget är när det inte går att konvertera kolumnen till en annan typ.  
  
 Det här felet i Azure Machine Learning inträffar när det inte går att konvertera kolumnen till den angivna typen.  Det här felet visas om en modul kräver en viss typ, som datetime, text, ett flyttal eller heltal, men det går inte att konvertera en befintlig kolumn till den begärda typen.  
 
Du kan exempelvis markera en kolumn och försök att konvertera den till en numerisk datatyp för användning i en matematiska åtgärden och få det här felet om kolumnen innehåller ogiltiga data. 

En annan orsak som du kan få det här felet om du försöker använda en kolumn som innehåller flyttal eller många unika värden som en kategoriska kolumn. 
  
**Lösning:**

+ Öppna på hjälpsidan för den modul som gav upphov till felet och kontrollera datatypskrav.
+ Granska datatyperna för kolumnerna i datauppsättningen för indata.
+ Granska data med ursprung i så kallade schemalösa datakällor.
+ Kontrollera datauppsättningen för värden som saknas eller specialtecken som kan blockera konvertering till datatypen önskade. 
    + Numeriska datatyper ska vara konsekvent: Kontrollera till exempel flyttal i en kolumn med heltal.
    + Leta efter textsträngar eller NA värden i en kolumn med tal. 
    + Booleska värden kan konverteras till en lämplig representation beroende på vilken typ av data som krävs.
    + Granska textkolumner för icke-Unicode-tecken, tabbtecken eller kontrolltecken
    + Datetime-data ska vara konsekvent att undvika modellering fel, men rensningen kan vara komplexa på grund av många format. Överväg att använda <!--the [Execute R Script](execute-r-script.md) or -->[Köra Python-skriptet](execute-python-script.md) moduler för att rensa.  
+ Om det behövs ändrar du värdena i den inkommande datauppsättningen så att kolumnen kan konverteras har. Ändring av kan innehålla datagruppering, trunkering eller avrundning-åtgärder, eliminering av extremvärden eller uppräkning av värden som saknas. Se följande artiklar för några vanliga data transformation scenarier i machine learning:
    + [Rensa Data som saknas](clean-missing-data.md)
    + [Normalisera Data](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Matchning oklart eller inte tillämpligt för ditt ärende? Är du Välkommen att skicka feedback på den här artikeln och ange information om scenariot med modulen och datatypen för kolumnen. Vi använder den här informationen för att tillhandahålla mer detaljerade felsökningsanvisningar i framtiden.  
  
|Undantag meddelanden|  
|------------------------|  
|Inte tillåtet att konverteringen.|  
|Det gick inte att konvertera en kolumn av typen {0} till kolumn av typen {1}.|  
|Det gick inte att konvertera kolumnen ”{2}” av typen {0} till kolumn av typen {1}.|  
|Det gick inte att konvertera kolumnen ”{2}” av typen {0} till kolumn ”{3}” av typen {1}.|  
  

## <a name="error-0043"></a>Error 0043  
 Undantaget är när elementtyp inte implementerar uttryckligen är lika med.  
  
 Det här felet i Azure Machine Learning är oanvänd och upphör att gälla.  
  
**Lösning:** Ingen.  
  
|Undantag meddelanden|  
|------------------------|  
|Ingen tillgänglig explicit metod är lika med hittades.|  
|Det går inte att jämföra värden för kolumnen \\”{0}\\” av typen {1}. Ingen tillgänglig explicit metod är lika med hittades.|  


## <a name="error-0044"></a>Error 0044  
 Undantaget är när det inte går att härleda för kolumnen från de befintliga värdena.  
  
 Det här felet i Azure Machine Learning uppstår när det inte går att härleda typen för en kolumn eller kolumner i en datauppsättning. Detta inträffar vanligtvis när sammanfoga två eller flera datauppsättningar med olika elementtyper. Om Azure Machine Learning kan inte fastställa en gemensam typ som kan representera alla värden i en kolumn eller kolumner utan förlust av information, genererar det här felet.  
  
**Lösning:** Se till att alla värden i en viss kolumn i båda datauppsättningar som kombineras är något av samma skriver (numeriska, booleskt, kategoriska, sträng, datum osv.) eller kan tvingas till samma typ.  
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att härleda för kolumnen.|  
|Det går inte att härleda elementtypen för kolumnen ”{0}” – alla element är null-referenser.|  
|Det går inte att härleda elementtypen för kolumnen ”{0}” i datauppsättning ”{1}” – alla element är null-referenser.|  
  

## <a name="error-0045"></a>Error 0045  
 Undantaget är när det inte går att skapa en kolumn på grund av blandat elementtyper i källan.  
  
 Det här felet i Azure Machine Learning skapas när de elementtyperna av två datauppsättningar som kombineras är olika.  
  
**Lösning:** Kontrollera att alla värden i en viss kolumn i båda datauppsättningar som kombineras är av samma typ (numeriska, booleskt, kategoriska, string, datum, osv.).  
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att skapa kolumn med blandad elementtyper.|  
|Det går inte att skapa kolumn med ID ”{0}” av blandat element typer: \n\tType av data [{1}, {0}] är {2}\n\tType av data [{3}, {0}] är {4}.|  
  

## <a name="error-0046"></a>Fel 0046  
 Undantaget är när det inte går att skapa katalog på den angivna sökvägen.  
  
 Det här felet i Azure Machine Learning uppstår när det inte går att skapa en katalog på den angivna sökvägen. Du får det här felet om någon del av sökvägen till katalogen för en Hive-fråga är felaktig eller otillgänglig.  
  
**Lösning:** Gå tillbaka till modulen och kontrollera att sökvägen har formaterats korrekt och att den är tillgänglig med de aktuella autentiseringsuppgifterna.  
  
|Undantag meddelanden|  
|------------------------|  
|Ange en giltig utdatakatalog.|  
|Katalog: {0} kan inte skapas. Ange giltig sökväg.|  
  

## <a name="error-0047"></a>Fel 0047  
 Undantaget är om antalet funktionen kolumner i några av de datauppsättningar som skickats till modulen är för liten.  
  
 Det här felet i Azure Machine Learning uppstår om datauppsättningen för indata till utbildning inte innehåller det minsta antalet kolumner som krävs av algoritmen. Vanligtvis antingen datauppsättningen är tom eller innehåller endast utbildning kolumner.  
  
**Lösning:** Gå tillbaka till den inkommande datauppsättningen för att se till att det finns en eller flera ytterligare kolumner förutom etikettkolumnen.  
  
|Undantag meddelanden|  
|------------------------|  
|Antalet kolumner i funktionen i datauppsättningen för indata är mindre än det tillåtna minimivärdet.|  
|Antalet kolumner i funktionen i datauppsättningen för indata är mindre än det tillåtna minimivärdet för {0} kolumn(er).|  
|Antalet kolumner i funktionen i datauppsättningen för indata ”{0}” är mindre än det tillåtna minimivärdet {1} kolumn(er).|  
  

## <a name="error-0048"></a>Fel 0048  
 Undantaget i fall när det inte går att öppna en fil.  
  
 Det här felet i Azure Machine Learning inträffar när det inte går att öppna en fil för läsning eller skriva. Du kan få det här felet därmed:  
  
-   Behållaren eller fil (blob) finns inte  
  
-   Åtkomstnivån för filen eller behållaren tillåter inte dig att komma åt filen  
  
-   Filen är för stor för Läs- eller fel format  
  
**Lösning:** Gå tillbaka till modulen och filen som du försöker läsa.  
  
 Kontrollera att namnen på behållaren och filen är korrekta.  
  
 Använda den klassiska Azure-portalen eller ett Azure storage-verktyg för att kontrollera att du har behörighet att komma åt filen.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att öppna en fil.|  
|Fel när filen öppnades: {0}.|  


## <a name="error-0049"></a>Error 0049  
 Undantaget i fall när det inte går att parsa en fil.  
  
 Det här felet i Azure Machine Learning inträffar när det inte går att parsa en fil. Du får det här felet om filformatet som valts i den [importdata](import-data.md) modulen matchar inte det aktuella formatet på filen, eller om filen innehåller ett okänt tecken.  
  
**Lösning:** Gå tillbaka till modulen och korrigera formatet filval om det inte matchar formatet på filen. Om möjligt granska filen för att bekräfta att den inte innehåller några ogiltiga tecken.  
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att parsa en fil.|  
|Fel vid parsning av filen: {0}.|  
  

## <a name="error-0050"></a>Fel 0050  
 Undantag som uppstår i fall när indata och utdatafilerna är samma.  
  
**Lösning:** Det här felet i Azure Machine Learning är oanvänd och upphör att gälla.  
  
|Undantag meddelanden|  
|------------------------|  
|Angivna filer för indata och utdata får inte vara samma.|


## <a name="error-0051"></a>Fel 0051  
 Undantaget i fall när flera utdatafilerna är samma.  
  
**Lösning:** Det här felet i Azure Machine Learning är oanvänd och upphör att gälla.  
  
|Undantag meddelanden|  
|------------------------|  
|Angivna filer för utdata får inte vara samma.|


## <a name="error-0052"></a>Fel 0052  
 Undantaget är om Azure storage-kontonyckel har angetts felaktigt.  
  
 Det här felet i Azure Machine Learning inträffar om den nyckel som används för att få åtkomst till Azure storage-kontot är felaktigt. Du kan till exempel se det här felet om Azure-lagringsnyckel trunkerades när kopieras och klistras in, eller om fel nyckel har använts.  
  
 Mer information om hur du hämtar nyckeln för ett Azure storage-konto finns i [visa, kopiera, och återskapa åtkomstnycklar](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Lösning:** Gå tillbaka till modulen och kontrollera att Azure-lagringsnyckel stämmer för kontot Kopiera nyckeln igen från den klassiska Azure-portalen om det behövs.  
  
|Undantag meddelanden|  
|------------------------|  
|Azure storage-kontonyckel är felaktigt.|  
  

## <a name="error-0053"></a>Fel 0053  
 Undantaget i fall när det finns inga funktioner eller objekt matchbox rekommendationer.  
  
 Det här felet i Azure Machine Learning skapas när en funktion vector inte kan hittas.  
  
**Lösning:** Se till att en funktion vector finns i datauppsättningen för indata.  
  
|Undantag meddelanden|  
|------------------------|  
|Funktioner för eller / och objekt krävs men har inte angetts.|  

## <a name="error-0054"></a>Error 0054  
 Undantaget är om det finns för få distinkta värden i kolumnen för att slutföra åtgärden.  
  
**Lösning:** Det här felet i Azure Machine Learning är oanvänd och upphör att gälla.  
  
|Undantag meddelanden|  
|------------------------|  
|Data har för få distinkta värden i den angivna kolumnen att slutföra åtgärden.|  
|Data har för få distinkta värden i den angivna kolumnen att slutföra åtgärden. Den måste vara minst {0} element.|  
|Data har för få distinkta värden i kolumnen ”{1}” att slutföra åtgärden. Den måste vara minst {0} element.|  
  

## <a name="error-0055"></a>Error 0055  
 Undantaget är när du anropar en föråldrad modul.  Det här felet i Azure Machine Learning visas om du försöker anropa en modul som är inaktuell.
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Åtkomst till föråldrad modulen.|  
|Modulen ”{0}” är inaktuell.|  

## <a name="error-0056"></a>Error 0056  
 Undantaget är om de kolumner som du har valt för en åtgärd strider mot kraven.  
  
 Det här felet i Azure Machine Learning uppstår när du väljer kolumner för en åtgärd som kräver kolumnen vara av en viss datatyp. 
 
 Det här felet kan också inträffa om kolumnen är rätt datatyp, men modulen som du använder kräver att kolumnen också vara markerad som en funktion, en etikett eller en kategoriska kolumn.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Lösning:**
  
1.  Granska datatypen för kolumnerna som är markerade. 

2. Fastställa om de markerade kolumnerna är kategoriska, etikett eller funktionen kolumner.  
  
3.  Granska hjälpavsnittet för modulen som du har gjort Kolumnurval att avgöra om det finns särskilda krav för dataanvändning för typen eller kolumn.  
  
3.  Använd [redigera Metadata](edit-metadata.md) ändra kolumntypen för varaktigheten för den här åtgärden. Kom ihåg att ändra kolumntypen tillbaka till det ursprungliga värdet, med hjälp av en annan instans av [redigera Metadata](edit-metadata.md)om du behöver den för underordnade åtgärder.  
  
|Undantag meddelanden|  
|------------------------|  
|En eller flera valda kolumner befann inte sig i ett tillåtet kategori.|  
|Kolumn med namnet ”{0}” är inte i en tillåtna kategori.|  
  

## <a name="error-0057"></a>Fel 0057  
 Undantaget är när du försöker skapa en fil eller en blob som redan finns.  
  
 Det här undantaget är när du använder den [exportera Data](export-data.md) modul eller andra moduler att spara resultatet av ett experiment i Azure Machine Learning i Azure blob-lagring, men du försöker skapa en fil eller en blob som redan finns.   
  
**Lösning:**
 
 Du får det här felet bara om du tidigare har egenskapen **Azure blob-lagring skrivläge** till **fel**. Enligt design genererar den här modulen ett fel om du försöker att skriva en datauppsättning till en blob som redan finns.
 
 - Öppna egenskaperna för modulen och ändra egenskapen **Azure blob-lagring skrivläge** till **överskrivning**.
 - Du kan också skriver du namnet på en annan målblobben eller filen och se till att ange en blob som inte redan finns.  
  
|Undantag meddelanden|  
|------------------------|  
|Fil- eller Blob finns redan.|  
|Fil- eller Blob ”{0}” finns redan.|  
  

## <a name="error-0058"></a>Error 0058  
 Det här felet i Azure Machine Learning uppstår om datauppsättningen inte innehåller den förväntade etikettkolumnen.  
  
 Det här undantaget kan även uppstå om etikettkolumnen som inte matchar de data eller datatyp som förväntas av eleven eller har fel värden. Till exempel skapas det här undantaget när du använder en real enkelvärdesattribut etikett kolumn vid utbildning av en binär klassificerare.  
  
**Lösning:** Lösningen är beroende av eleven eller trainer som du använder och datatyperna för kolumnerna i din datauppsättning. Kontrollera först kraven i machine learning-algoritm eller utbildning modulen.  
  
 Gå tillbaka till den inkommande datauppsättningen. Kontrollera att kolumnen du förväntar dig att behandlas som etiketten har rätt datatyp för den modell som du skapar.  
  
 Kontrollera indata för värden som saknas och eliminera eller ersätta dem om det behövs.  
  
 Om det behövs lägger du till den [redigera Metadata](edit-metadata.md) modulen och kontrollera att etikettkolumnen är markerad som en etikett.  
  
|Undantag meddelanden|  
|------------------------|  
|Etikettkolumnen är inte som förväntat|  
|Etikettkolumnen är inte som förväntat i ”{0}”.|  
|Etikettkolumnen ”{0}” förväntas inte i ”{1}”.|  
  

## <a name="error-0059"></a>Fel 0059  
 Undantaget är om en kolumnindex som anges i en kolumn Väljaren inte kan parsas.  
  
 Det här felet i Azure Machine Learning inträffar om en kolumnindex som anges när du använder kolumnväljaren inte kan parsas.  Du får det här felet när kolumnindexet är ett ogiltigt format som inte kan parsas.  
  
**Lösning:** Ändra kolumnindex om du vill använda ett giltigt index-värde.  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att parsa en eller flera angivna kolumnindex eller index intervall.|  
|Kolumnindex eller ett intervall ”{0}” kunde inte parsas.|  
  

## <a name="error-0060"></a>Fel 0060  
 Undantaget är när en out of intervallet kolumnen intervallet anges i en kolumnväljare.  
  
 Det här felet i Azure Machine Learning inträffar när ett out-stapelintervall adressintervall har angetts i kolumnväljaren. Du får det här felet om kolumnen intervallet i väljaren för kolumnen inte motsvarar kolumner i datauppsättningen.  
  
**Lösning:** Ändra intervallet för kolumnen i Väljaren kolumnen som motsvarar kolumnerna i datauppsättningen.  
  
|Undantag meddelanden|  
|------------------------|  
|Ogiltigt eller utanför intervallet kolumnen index intervallet anges.|  
|Intervall för kolumnen ”{0}” är ogiltigt eller utanför intervallet.|  
  

## <a name="error-0061"></a>Fel 0061  
 Undantaget är när du försöker lägga till en rad i en DataTable som har olika antal kolumner än tabellen.  
  
 Det här felet i Azure Machine Learning uppstår när du försöker lägga till en rad i en datauppsättning som har olika antal kolumner än datauppsättningen.  Du får det här felet om raden som du vill lägga till datamängden som har olika antal kolumner från datauppsättningen för indata.  Raden kan inte läggas till datauppsättningen om antalet kolumner är olika.  
  
**Lösning:** Ändra den inkommande datauppsättningen för att ha samma antal kolumner som den rad som har lagts till eller ändra den raden som lagts till i ha samma antal kolumner som datamängd.  
  
|Undantag meddelanden|  
|------------------------|  
|Alla tabeller måste ha samma antal kolumner.|  
  

## <a name="error-0062"></a>Error 0062  
 Undantag inträffar vid försök att jämföra två modeller med olika learner typer.  
  
 Det här felet i Azure Machine Learning skapas när utvärderingsmått för två olika poängsatta datauppsättningar kan jämföras. I det här fallet går det inte att jämföra effektiviteten i modeller som användes för att skapa två poängsatta datauppsättningar.  
  
**Lösning:** Kontrollera att poängsatta resultaten som produceras av samma typ av machine learning-modell (binär klassificering, regression, flera klassificering, rekommendation, klustring, avvikelseidentifiering, osv.) Alla modeller som du jämföra måste ha samma learner typ.  
  
|Undantag meddelanden|  
|------------------------|  
|Alla modeller måste ha samma learner typ.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Undantag meddelanden|  
|------------------------|  
|Fel vid utvärdering av R-skript.|  
|Följande fel uppstod under utvärderingen av R-skript:---början av felmeddelande från R--- {0} ---slut felmeddelande från R---|  
|Under utvärderingen av R-skriptet ”{1}” följande fel uppstod:---början av felmeddelande från R--- {0} ---slut felmeddelande från R---|  
  


## <a name="error-0064"></a>Error 0064  
 Undantaget är om Azure storage-kontonamn eller nyckel har angetts felaktigt.  
  
 Det här felet i Azure Machine Learning uppstår om Azure storage-kontonamn eller nyckel har angetts felaktigt. Du får det här felet om du anger ett felaktigt kontonamn eller lösenord för lagringskontot. Detta kan inträffa om du manuellt ange kontonamn eller lösenord. Det kan också inträffa om kontot har tagits bort.  
  
**Lösning:** Kontrollera att kontonamnet och lösenordet har angetts på rätt sätt och att kontot finns.  
  
|Undantag meddelanden|  
|------------------------|  
|Azure storage-kontonamn eller lagringsnyckeln är felaktigt.|  
|Namnet på Azure storage-kontot ”{0}” eller lagringsnyckel för kontonamnet är felaktig.|  
  

## <a name="error-0065"></a>Error 0065  
 Undantaget är om Azure blobnamn har angetts felaktigt.  
  
 Det här felet i Azure Machine Learning uppstår om Azure blob-namnet har angetts felaktigt.  Du får felet om:  
  
-   Blob kan inte hittas i den angivna behållaren.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Endast behållaren har angetts som i en [importdata](import-data.md) begär när formatet Excel eller CSV med kodning; sammanfogning av innehållet i alla blobar i en behållare är inte tillåtet med dessa format.  
  
-   En SAS-URI innehåller inte namnet på en giltig blob.  
  
**Lösning:** Gå tillbaka till den modul som utlöste undantaget. Kontrollera att den angivna blobben finns i behållaren i storage-kontot och att behörigheter kan du se blob. Kontrollera att indata är i formatet **containername/filename** om du har Excel eller CSV med kodning format. Kontrollera att en SAS-URI innehåller namnet på en giltig blob.  
  
|Undantag meddelanden|  
|------------------------|  
|Azure storage-blob är felaktig.|  
|Azure storage blob-namnet ”{0}” är felaktig|  
  

## <a name="error-0066"></a>Fel 0066  
 Undantaget är om en resurs inte kunde överföras till en Azure-Blob.  
  
 Det här felet i Azure Machine Learning inträffar om en resurs inte kunde överföras till en Azure-Blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Båda sparas i samma Azure storage-kontot som det konto som innehåller indatafilen.  
  
**Lösning:** Gå tillbaka till modulen. Kontrollera att Azure-kontonamn, nyckel och behållare är korrekta och att kontot har behörighet att skriva till behållaren.  
  
|Undantag meddelanden|  
|------------------------|  
|Resursen kunde inte överföras till Azure storage.|  
|Filen ”{0}” kunde inte överföras till Azure storage som {1}.|  
  

## <a name="error-0067"></a>Fel 0067  
 Undantaget är om en datauppsättning har olika antal kolumner än förväntat.  
  
 Det här felet i Azure Machine Learning inträffar om en datauppsättning har olika antal kolumner än förväntat.  Du får det här felet när antalet kolumner i datauppsättningen skiljer sig från antalet kolumner som modulen förväntar sig under körning.  
  
**Lösning:** Ändra den inkommande datauppsättningen eller parametrarna.  
  
|Undantag meddelanden|  
|------------------------|  
|Ett oväntat antal kolumner i funktionen datatable.|  
|Förväntat ”{0}” kolumner hittades men ”{1}” kolumner i stället.|  
  

## <a name="error-0068"></a>Fel 0068  
 Undantaget är om det angivna Hive-skriptet inte är korrekt.  
  
 Det här felet i Azure Machine Learning uppstår om det finns syntaxfel i en Hive SQL-skript, eller om Hive-tolk påträffar ett fel vid körning av fråga eller skript.  
  
**Lösning:**

Felmeddelandet från Hive rapporteras normalt tillbaka i felloggen så att du kan vidta åtgärder baserat på det specifika felet. 

+ Öppna modulen och granska frågan för misstag.  
+ Kontrollera att frågan fungerar utanför Azure Machine Learning genom att logga in till Hive-konsolen på Hadoop-klustret och köra frågan.  
+ Försök att placera kommentarer i Hive-skript i en separat rad till skillnad från blanda körbara instruktioner och kommentarer i en enda rad.  

### <a name="resources"></a>Resurser

Se följande artiklar om du behöver hjälp med Hive-frågor för machine learning:

+ [Skapa Hive-tabeller och läsa in data från Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Utforska data i tabeller med Hive-frågor](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Skapa funktioner för data i ett Hadoop-kluster med hjälp av Hive-frågor](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive för facit för SQL-användare (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Undantag meddelanden|  
|------------------------|  
|Hive-skriptet är felaktigt.|  
|Registreringsdatafilen {0} stämmer inte.|  
  

## <a name="error-0069"></a>Fel 0069  
 Undantaget är om det angivna SQL-skriptet inte är korrekt.  
  
 Det här felet i Azure Machine Learning uppstår om det angivna SQL-skriptet har syntax problem, eller om kolumner eller tabell som anges i skriptet är inte giltig. 
 
 Du får det här felet om SQL-motor påträffar ett fel vid körning av fråga eller skript. SQL-felmeddelande rapporteras normalt tillbaka i felloggen så att du kan vidta åtgärder baserat på det specifika felet.  
  
**Lösning:** Gå tillbaka till modulen och granska SQL-fråga för misstag.  
  
 Kontrollera att frågan fungerar utanför Azure ML genom att logga in direkt på databasservern och köra frågan.  
  
 Om det finns ett meddelande för SQL genereras som rapporterats av modulen undantaget, fatta beslut utifrån det rapporterade felet. Till exempel innehåller felmeddelandena ibland specifika vägledningar om det troliga fel:
+ *Det finns ingen sådan kolumn eller saknas databasen*, som anger att du kan ha angett ett kolumnnamn som är fel. Om du är säker kolumnens namn är korrekt, försök med hakparenteser eller citattecken runt kolumn-ID.
+ *SQL-logikfel nära \<SQL-nyckelord\>*, som anger att det finns ett syntaxfel innan det angivna nyckelordet

  
|Undantag meddelanden|  
|------------------------|  
|SQL-skriptet är felaktigt.|  
|SQL-fråga ”{0}” är inte korrekt.|  
|SQL-fråga ”{0}” är inte korrekt: {1}|  
  

## <a name="error-0070"></a>Fel 0070  
 Undantaget är när du försöker få åtkomst till icke-existerande Azure-tabell.  
  
 Det här felet i Azure Machine Learning uppstår när du försöker komma åt en icke-existerande Azure-tabell. Du får det här felet om du anger en tabell i Azure storage, det inte finns under läsning från eller skriva till Azure Table Storage. Detta kan inträffa om du skriver namnet på önskad tabell, eller om du har ett matchningsfel mellan namnet på målet och lagringstypen. Till exempel avsedd att läsa från en tabell men har angett namnet på en blob i stället.  
  
**Lösning:** Gå tillbaka till modulen för att kontrollera att namnet på tabellen är korrekt.  
  
|Undantag meddelanden|  
|------------------------|  
|Azure-tabellen finns inte.|  
|Azure-tabell ”{0}” finns inte.|  
  
## <a name="error-0071"></a>Fel 0071  
 Undantaget är om autentiseringsuppgifterna är felaktiga.  
  
 Det här felet i Azure Machine Learning uppstår om de angivna autentiseringsuppgifterna är felaktiga.  
  
 Du kan också få det här felet om modulen inte kan ansluta till ett HDInsight-kluster.  
  
**Lösning:** Granska indata till modulen och Kontrollera kontonamnet och lösenordet.  
  
 Sök efter följande problem som kan orsaka ett fel:  
  
-   Schemat för datauppsättningen matchar inte schemat för funktionen datatable mål.  
  
-   Kolumnnamnen är saknas eller är felstavade  
  
-   Du skriver till en tabell som innehåller kolumnnamn med ogiltiga tecken. Normalt kan du ange sådana kolumnnamn inom hakparenteser och om det inte fungerar kan du redigera kolumnnamn för att använda endast bokstäver och understreck (_)  
  
-   Innehåller strängarna som du försöker att skriva enkla citattecken  
  
 Om du försöker ansluta till ett HDInsight-kluster, kontrollerar du att målklustret är tillgänglig med de angivna autentiseringsuppgifterna.  
  
|Undantag meddelanden|  
|------------------------|  
|Felaktiga autentiseringsuppgifter skickas.|  
|Felaktigt användarnamn ”{0}” eller lösenord skickas|  
  

## <a name="error-0072"></a>Fel 0072  
 Undantaget är när det gäller tidsgräns för anslutning.  
  
 Det här felet i Azure Machine Learning inträffar när en anslutning. Det här felet visas om det finns för närvarande anslutningsproblem med datakällan eller mål, till exempel långsam anslutning till internet, eller om datauppsättningen är stor och/eller SQL-frågan ska lästa data utför avancerad bearbetning.  
  
**Lösning:** Kontrollera om det finns för närvarande problem med långsam anslutning till Azure storage eller internet.  
  
|Undantag meddelanden|  
|------------------------|  
|Det uppstod en timeout för anslutning.|  
  

## <a name="error-0073"></a>Fel 0073  
 Undantaget är om ett fel inträffar vid konvertering av en kolumn till en annan typ.  
  
 Det här felet i Azure Machine Learning inträffar när det inte går att konvertera kolumnen till en annan typ.  Du får det här felet om en modul kräver en viss typ och det går inte att konvertera kolumnen till den nya typen.  
  
**Lösning:** Ändra den inkommande datauppsättningen så att kolumnen kan konverteras baserat på det inre undantaget.  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att konvertera kolumnen.|  
|Det gick inte att konvertera kolumnen till {0}.|  
  

## <a name="error-0074"></a>Error 0074  
 Undantaget är när den [redigera Metadata](edit-metadata.md) försöker konvertera ett null-optimerad kolumn till kategoriska.  
  
 Det här felet i Azure Machine Learning uppstår när den [redigera Metadata](edit-metadata.md) försöker konvertera ett null-optimerad kolumn till kategoriska.  Du får det här felet när du försöker konvertera null-optimerade kolumner till kategoriska med den **gör kategoriska** alternativet.  Azure machine Learning stöder inte null-optimerade kategoriska matriser, så att modulen fel.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Undantag meddelanden|  
|------------------------|  
|Null-optimerade kolumner kan inte konverteras till Kategoriskt.|  
  

## <a name="error-0075"></a>Fel 0075  
Undantaget är när en ogiltig datagrupperingen-funktion som ska användas när quantizing en datauppsättning.  
  
Det här felet i Azure Machine Learning uppstår när du försöker bin data med hjälp av en metod som inte stöds, eller när kombinationerna av parametrar är ogiltiga.  
  
**Lösning:**

Felhantering för den här händelsen introducerades i en tidigare version av Azure Machine Learning som får mer anpassning av diskretisering metoder. För närvarande baseras alla datagrupperingen metoder på ett urval från en listruta tekniskt det bör inte längre går att få det här felet.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Undantag meddelanden|  
|------------------------|  
|Ogiltig datagrupperingen funktion som används.|  
  

## <a name="error-0077"></a>Fel 0077  
 Undantaget är när okända blob-fil skriver läge skickas.  
  
 Det här felet i Azure Machine Learning uppstår om ett ogiltigt argument skickades specifikationerna för en blob-filmål- eller källenheten.  
  
**Lösning:** I nästan alla moduler som du importera eller exportera data till och från Azure blob storage, tilldelas parametervärden som styr skrivläge med hjälp av en listruta; Därför kan det går inte att skicka ett ogiltigt värde och det här felet ska inte visas. Det här felet kommer att inaktualiseras i en senare version.  
  
|Undantag meddelanden|  
|------------------------|  
|Stöds inte blob skriver läge.|  
|Stöds inte blob skriver läge: {0}.|  
  

## <a name="error-0078"></a>Fel 0078  
 Undantaget är när HTTP alternativet för [importdata](import-data.md) tar emot statuskoden 3xx som anger omdirigering.  
  
 Det här felet i Azure Machine Learning uppstår när HTTP alternativet för [importdata](import-data.md) tar emot en 3xx (301, 302, 304, etc.) statuskod som visar omdirigering. Du får det här felet om du försöker ansluta till en HTTP-källa som dirigerar om webbläsaren till en annan sida. För säkerhet är orsaker, omdirigera webbplatser inte tillåtna som datakällor för Azure Machine Learning.  
  
**Lösning:** Om webbplatsen är en betrodd webbplats, anger du den omdirigerade platsen direkt.  
  
|Undantag meddelanden|  
|------------------------|  
|HTTP-omdirigering inte tillåtet|  
  

## <a name="error-0079"></a>Fel 0079  
 Undantaget är om Azure storage-behållarnamn har angetts felaktigt.  
  
 Det här felet i Azure Machine Learning uppstår om namnet på Azure storage-behållare har angetts felaktigt. Du får det här felet om du inte har angett behållaren och blob (fil) namn med **sökvägen till blob som börjar med behållare** alternativet när du skriver till Azure Blob Storage.  
  
**Lösning:** Gå tillbaka till den [exportera Data](export-data.md) modulen och kontrollera att den angivna sökvägen till blobben innehåller både behållaren och filnamnet i formatet **behållare/filename**.  
  
|Undantag meddelanden|  
|------------------------|  
|Azure storage-behållarens namn är felaktigt.|  
|Namnet på Azure storage-behållare ”{0}” är felaktigt; ett behållarnamn på formatet behållare/blob förväntades.|  
  

## <a name="error-0080"></a>Error 0080  
 Undantaget är när kolumnen med alla värden som saknas inte är tillåten i modulen.  
  
 Det här felet i Azure Machine Learning skapas när en eller flera av de kolumner som används av modulen innehåller alla värden som saknas. Till exempel om en modul är databehandling sammanställd statistik för varje kolumn, fungerar den inte på en kolumn som innehåller inga data. I sådana fall kan modulen stannar upp med det här undantaget.  
  
**Lösning:** Gå tillbaka till den inkommande datauppsättningen och ta bort alla kolumner som innehåller alla värden som saknas.  
  
|Undantag meddelanden|  
|------------------------|  
|Kolumner med alla värden som saknas är inte tillåtna.|  
|Kolumnen {0} har alla värden som saknas.|  
  

## <a name="error-0081"></a>Error 0081  
 Undantaget i PCA-modulen om antalet dimensioner att minska till är lika med antalet funktionen kolumner i datauppsättningen för indata, som innehåller minst en null-optimerade funktionen kolumn.  
  
 Det här felet i Azure Machine Learning skapas om följande villkor är uppfyllda: (a) den inkommande datauppsättningen har minst en null-optimerad kolumn och (b) det sista antalet dimensioner som begärdes är detsamma som antalet inkommande dimensioner.  
  
**Lösning:** Överväg att minska antalet dimensioner i utdata ska vara färre än antalet dimensioner i indata. Det här är vanligt i PCA-program.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Undantag meddelanden|  
|------------------------|  
|För datauppsättningen som innehåller funktionen för null-optimerade kolumner vara dimensioner för att minska antalet mindre än antalet kolumner i funktionen.|  
 

## <a name="error-0082"></a>Error 0082  
 Undantaget är när en modell inte kan vara har avserialiserade.  
  
 Det här felet i Azure Machine Learning inträffar när en sparad machine learning-modell eller transformeringen kan inte läsas in av en nyare version av Azure Machine Learning-körningen på grund av en stor förändring.  
  
**Lösning:** Träningsexperimentet som producerade modellen eller transformeringen måste vara kör och modellen eller transformeringen måste spara.  
  
|Undantag meddelanden|  
|------------------------|  
|Gick inte att deserialisera modellen eftersom den sannolikt serialiseras med en äldre serialiseringsformat. Omtrimma och spara om modellen.|  
  

## <a name="error-0083"></a>Error 0083  
 Undantaget är om datauppsättningen som används för utbildning inte kan användas för konkret typ av learner.  
  
 Det här felet i Azure Machine Learning skapas när datauppsättningen är inte kompatibel med eleven håller på att tränas. Till exempel datauppsättningen kan innehålla minst ett värde på varje rad som saknas och hela datamängden skulle därför att åsidosättas vid utbildning. I annat fall vissa machine learning-algoritmer, till exempel avvikelseidentifiering förväntar sig inte etiketter måste finnas och orsaka det här undantaget om etiketter finns i datauppsättningen.  
  
**Lösning:** I dokumentationen för eleven som används för att kontrollera kraven för datauppsättningen för indata. Granska kolumner om du vill se alla nödvändiga kolumner finns.  
  
|Undantag meddelanden|  
|------------------------|  
|Datauppsättning som används för träning är ogiltig.|  
|{0} innehåller ogiltiga data för utbildning.|  
|{0} innehåller ogiltiga data för utbildning. Learner typ: {1}.|  
  

## <a name="error-0084"></a>Error 0084  
 Undantaget är när du poäng som genereras från ett R-skript utvärderas. Det här är för närvarande stöds inte.  
  
 Det här felet i Azure Machine Learning uppstår om du försöker använda en av modulerna för att utvärdera en modell med utdata från ett R-skript som innehåller poäng.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att utvärdera poäng som produceras av R.|  
  

## <a name="error-0085"></a>Error 0085  
 Undantaget är när utvärderingen av skriptet misslyckas med ett fel.  
  
 Det här felet i Azure Machine Learning inträffar när du använder ett anpassat skript som innehåller syntaxfel.  
  
**Lösning:** Granska koden i en extern redigerare och Sök efter fel.  
  
|Undantag meddelanden|  
|------------------------|  
|Fel vid utvärdering av skriptet.|  
|Följande fel uppstod under utvärdering av skriptet, visa utdataloggen för mer information:---början av felmeddelande från {0} tolk--- {1} ---slut felmeddelande från {0} tolk--- ------|  
  

## <a name="error-0086"></a>Error 0086  
 Undantaget är när en cykliska transformeringen är ogiltig.  
  
 Det här felet i Azure Machine Learning uppstår när du väljer en transformation som baseras på ett antal tabeller, men den valda transformeringen är inte kompatibel med aktuella data eller med den nya count-tabellen.  
  
**Lösning:** Har stöd för modulen sparar antalet och regler som utgör omvandling i två olika format. Om du kopplar antal tabeller, kan du kontrollera att båda tabellerna som du vill slå samman använder samma format.  
  
I allmänhet kan en count-baserade transformering endast tillämpas på datauppsättningar som har samma schema som datamängd transformering ursprungligen skapades.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Undantag meddelanden|  
|------------------------|  
|Ogiltig cykliska transformeringen har angetts.|  
|Cykliska transformering på indataporten '{0}' är ogiltig.|  
|Cykliska transformering på indataporten '{0}'kan inte slås samman med cykliska transformering på indataporten'{1}'. Kontrollera för att kontrollera de metadata som används för att kunna räkna matchningar.|  
  

## <a name="error-0087"></a>Error 0087  
 Undantaget är när ett ogiltigt antal tabelltyp har angetts för inlärning med antal moduler.  
  
 Det här felet i Azure Machine Learning uppstår när du försöker importera en befintlig antal tabell, men tabellen är inte kompatibel med aktuella data eller med den nya count-tabellen.  
  
**Lösning:** Det finns olika format för att spara antalet och regler som utgör transformeringen. Om du kopplar antal tabeller, kan du kontrollera att båda använder samma format.  
  
 I allmänhet kan en count-baserade transformering endast tillämpas på datauppsättningar som har samma schema som datamängd transformering ursprungligen skapades.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Error 0088  
 Undantaget är när en ogiltig räkna typ har angetts för inlärning med antal moduler.  
  
 Det här felet i Azure Machine Learning uppstår vid försök att använda en annan cykliska metod än stöds för count-baserade funktionalisering.  
  
**Lösning:** I allmänhet cykliska metoder du väljer en listruta, så du inte bör se det här felet.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Undantag meddelanden|  
|------------------------|  
|Ogiltig räkna typ har angetts.|  
|Den angivna cykliska typen '{0}' är inte en giltig typ av fler är på gång.|  
  

## <a name="error-0089"></a>Error 0089  
 Undantaget är när det angivna antalet klasser är mindre än antalet klasser i en datauppsättning som används för inventering.  
  
 Det här felet i Azure Machine Learning uppstår när du skapar en count-tabell och etikettkolumnen innehåller ett annat antal klasser än du angav i modulparametrar.  
  
**Lösning:** Kontrollera din datauppsättning och ta reda på exakt hur många distinkta värden (möjliga klasser) finns i etikettkolumnen. När du skapar tabellen count, måste du ange minst detta antal klasser.  
  
 Tabellen antal kan inte automatiskt fastställa antal klasser som är tillgängliga.  
  
 När du skapar tabellen count, du kan inte ange 0 eller alla antalet som är mindre än antalet klasser i etikettkolumnen.  
  
|Undantag meddelanden|  
|------------------------|  
|Antal klasser är felaktigt. Se till att antalet klasser som du anger i fönstret parametrar är större än eller lika med antalet klasser i etikettkolumnen.|  
|Antal klasser som angetts är '{0}”, som inte är större än etikettvärdet för en”{1}”i den datamängd som används för att räkna. Se till att antalet klasser som du anger i fönstret parametrar är större än eller lika med antalet klasser i etikettkolumnen.|  
  

## <a name="error-0090"></a>Fel 0090  
 Undantaget är när inte går att skapa för Hive-tabell.  
  
 Det här felet i Azure Machine Learning uppstår när du använder [exportera Data](export-data.md) eller ett annat alternativ för att spara data till ett HDInsight-kluster och den angivna Hive-tabellen kan inte skapas.  
  
**Lösning:** Kontrollera namnet på Azure storage-kontot som är associerade med klustret och att du använder samma konto i egenskaperna för modulen.  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att skapa Hive-tabell. Kontrollera att namnet på Azure storage-kontot som är associerade med klustret är samma som det skickas via parametern modulen för ett HDInsight-kluster.|  
|Hive-tabellen ”{0}” kunde inte skapas. Kontrollera att namnet på Azure storage-kontot som är associerade med klustret är samma som det skickas via parametern modulen för ett HDInsight-kluster.|  
|Hive-tabellen ”{0}” kunde inte skapas. För ett HDInsight-kluster, se till att namnet på Azure storage-kontot som är associerade med klustret är ”{1}”.|  
 

## <a name="error-0100"></a>Fel 0100  
 Undantaget är när ett språk för som inte stöds har angetts för en anpassad modul.  
  
 Det här felet i Azure Machine Learning uppstår när du skapar en anpassad modul och name-egenskapen för den **språk** element i en anpassad modul xml-definitionsfilen har ett ogiltigt värde. Det enda giltiga värdet för den här egenskapen är för närvarande `R`. Exempel:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Lösning:** Kontrollera att namnegenskapen för den **språk** elementet i xml-definitionsfilen för anpassad modul har angetts till `R`. Spara filen, uppdatera anpassad modul zip-paketet och försök att lägga till anpassad modul igen.  
  
|Undantag meddelanden|  
|------------------------|  
|Språk som stöds inte anpassad modul har angetts|  
  

## <a name="error-0101"></a>Fel 0101  
 Alla port och parametern-ID: N måste vara unikt.  
  
 Det här felet i Azure Machine Learning uppstår när en eller flera portar eller parametrar har tilldelats samma ID-värde i en anpassad modul XML-definitionsfilen.  
  
**Lösning:** Kontrollera att ID-värden över alla portar och parametrar är unika. Spara xml-filen, uppdatera anpassad modul zip-paketet och försök att lägga till anpassad modul igen.  
  
|Undantag meddelanden|  
|------------------------|  
|Alla port och parametern ID: N för en modul måste vara unikt|  
|Modulen '{0}' har duplicerade port/argument ID: N. Alla port/argumentet ID: N måste vara unikt för en modul.|  
  

## <a name="error-0102"></a>Fel 0102  
 Ett felmeddelande när en ZIP-fil inte kan extraheras.  
  
 Det här felet i Azure Machine Learning uppstår när du importerar ett komprimerade paket med filnamnstillägget .zip, men paketet är antingen inte en zip-fil eller filen inte använder ett stöds zip-format.  
  
**Lösning:** Kontrollera att den valda filen är en giltig ZIP-fil och att den har komprimerats med någon av stöds komprimering algoritmerna.  
  
 Om du får det här felet när du importerar datauppsättningar i komprimerat format, kontrollera att alla filer med någon av filformat som stöds och är i Unicode-format.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Lägg till igen önskade filer till en ny komprimerad komprimerade mapp och försök att lägga till anpassad modul igen.  
  
|Undantag meddelanden|  
|------------------------|  
|Den angivna ZIP är filen inte i rätt format|  


## <a name="error-0103"></a>Fel 0103  
 Utlöses när en ZIP-filen inte innehåller några XML-filer  
  
 Det här felet i Azure Machine Learning inträffar när anpassad modul zip-paketet inte innehåller någon modul-definitionsfiler (.xml). De här filerna måste finnas i roten av zip-paketet (till exempel inte är i en undermapp.)  
  
**Lösning:** Verifiera att en eller flera definitionsfiler för XML-modulen finns i rotmappen på zip-paketet genom att extrahera det till en tillfällig mapp på enheten. XML-filer ska vara direkt i mappen som du extraherade zip-paket till. Kontrollera att när du skapar zip-paketet som du inte väljer en mapp som innehåller xml-filer som zip som en undermapp i zip-paketet skapas med samma namn som den mapp som du har valt för att zip.  
  
|Undantag meddelanden|  
|------------------------|  
|Angivna ZIP innehåller filen inte någon modul definitionsfiler (.xml-filer)|  


## <a name="error-0104"></a>Fel 0104  
 Utlöses när en modul-definitionsfil refererar till ett skript som inte finns  
  
 Det här felet i Azure Machine Learning genereras när en anpassad modul xml-definitionsfilen refererar till en skriptfil i den **språk** element som inte finns i zip-paketet. Sökväg till skriptfilen definieras i den **sourceFile** egenskapen för den **språk** element. Sökvägen till källfilen är i förhållande till roten för zip-paket (samma plats som modulen XML-definition-filer). Om skriptfilen finns i en undermapp, måste den relativa sökvägen till skriptfilen anges. Exempelvis om alla skript som har lagrats i en **myScripts** mappen i zip-paketet, den **språk** elementet skulle behöva lägga till den här sökvägen den **sourceFile** egenskapen som nedan. Exempel:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Lösning:** Se till att värdet för den **sourceFile** -egenskapen i den **språk** elementet i xml-definition för anpassad modul är korrekt och att källfilen finns i rätt relativ sökväg i zip-paketet.  
  
|Undantag meddelanden|  
|------------------------|  
|Refererade R-skriptfil finns inte.|  
|Refererade R-skriptfilen '{0}' kan inte hittas. Kontrollera att den relativa sökvägen till filen är korrekt var definitioner.|  


## <a name="error-0105"></a>Fel 0105  
 Det här felet visas när en definitionsfil för modulen innehåller en parametertyp  
  
 Det här felet i Azure Machine Learning skapas när du skapar en anpassad modul xml-definition och typen för en parameter eller argumentet i definitionen matchar inte en typ som stöds.  
  
**Lösning:** Se till att egenskapen type för någon **Arg** elementet i xml-definitionsfilen för anpassad modul är en typ som stöds.  
  
|Undantag meddelanden|  
|------------------------|  
|En parametertyp.|  
|Parametertypen stöds inte ”{0}” angivna.|  


## <a name="error-0106"></a>Fel 0106  
 Utlöses när en modul-definitionsfil definierar en typ av användarindata som inte stöds  
  
 Det här felet i Azure Machine Learning skapas när typ av en port i en anpassad modul XML-definition inte matchar en typ som stöds.  
  
**Lösning:** Se till att type-egenskapen för ett indata-element i XML-definitionsfilen för anpassad modul är en typ som stöds.  
  
|Undantag meddelanden|  
|------------------------|  
|Inkommande typen stöds inte.|  
|Stöds inte Indatatyp '{0}' angivna.|  


## <a name="error-0107"></a>Fel 0107  
 Utlöses när en modul-definitionsfil definierar en stöds inte Utdatatyp  
  
 Det här felet i Azure Machine Learning skapas när typ av en utgångsport i en anpassad modul xml-definition inte matchar en typ som stöds.  
  
**Lösning:** Se till att type-egenskapen för en Output-elementet i xml-definitionsfilen för anpassad modul är en typ som stöds.  
  
|Undantag meddelanden|  
|------------------------|  
|Utdatatyp som inte stöds.|  
|Parametertyp utdata '{0}' angivna.|  


## <a name="error-0108"></a>Fel 0108  
 Utlöses när en modul-definitionsfil definierar fler inkommande eller utgående portar än vad som stöds  
  
 Det här felet i Azure Machine Learning skapas när för många inkommande eller utgående portar har definierats i en anpassad modul xml-definition.  
  
**Lösning:** Ser till att det maximala antalet inkommande och utgående portar som definierats i xml-definitionen för anpassad modul inte överskrider det maximala antalet portar som stöds.  
  
|Undantag meddelanden|  
|------------------------|  
|Överskred tillåtna antalet inkommande eller utgående portar.|  
|Överskred antalet som stöds ”{0}” portar. Högsta antalet tillåtna '{0}portar är{1}'.| 

## <a name="error-0109"></a>Fel 0109  
 Utlöses när en modul-definitionsfil definierar en kolumnväljare felaktigt  
  
 Det här felet i Azure Machine Learning skapas när syntaxen för ett argument för väljare av kolumnen innehåller ett fel i en anpassad modul xml-definition.  
  
**Lösning:** Det här felet skapas när syntaxen för ett argument för väljare av kolumnen innehåller ett fel i en anpassad modul xml-definition.  
  
|Undantag meddelanden|  
|------------------------|  
|Syntax för kolumnväljare som inte stöds.|  
  

## <a name="error-0110"></a>Fel 0110  
 Utlöses när en modul-definitionsfil definierar en kolumnväljare som refererar till ett icke-existerande indataporten-ID  
  
 Det här felet i Azure Machine Learning skapas när den *portId* egenskap i Properties-elementet i en Arg av typen ColumnPicker matchar inte ID-värdet för en port.  
  
**Lösning:** Kontrollera att egenskapen portId matchar det ID-värdet för en inkommande port som definierats i xml-definitionen för anpassad modul.  
  
|Undantag meddelanden|  
|------------------------|  
|Kolumnväljare refererar till ett icke-existerande indataporten-ID.|  
|Kolumnväljare refererar till ett icke-existerande indataporten ID '{0}'.|  
  

## <a name="error-0111"></a>Fel 0111  
 Utlöses när en modul-definitionsfil definierar en ogiltig egenskap  
  
 Det här felet i Azure Machine Learning skapas när en ogiltig egenskap tilldelas till ett element i modulen anpassade XML-definition.  
  
**Lösning:** Kontrollera att egenskapen stöds av anpassad modul-elementet.  
  
|Undantag meddelanden|  
|------------------------|  
|Egenskapsdefinitionen är ogiltig.|  
|Egenskapsdefinition '{0}' är ogiltig.|  
  

## <a name="error-0112"></a>Fel 0112  
 Utlöses när en definitionsfil för modulen inte kan tolkas  
  
 Det här felet i Azure Machine Learning skapas när det finns ett fel i xml-format som förhindrar att modulen anpassade XML-definition från att parsas som en giltig XML-fil.  
  
**Lösning:** Se till att varje element öppnas och stängs korrekt. Se till att det finns några fel i XML-formatering.  
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att parsa filen för modulen definition.|  
|Det gick inte att parsa filen för modulen definition '{0}'.|  
  

## <a name="error-0113"></a>Fel 0113  
 Ett felmeddelande när en definitionsfil för modulen innehåller fel.  
  
 Det här felet i Azure Machine Learning skapas när modulen anpassade XML-definitionsfilen kan parsas men innehåller fel, till exempel definitionen av element som inte stöds av anpassade moduler.  
  
**Lösning:** Kontrollera att den anpassad modul definitionsfilen definierar element och egenskaper som stöds av anpassade moduler.  
  
|Undantag meddelanden|  
|------------------------|  
|Definition-filen för modulen innehåller fel.|  
|Modulen definitionsfilen '{0}' innehåller fel.|  
|Modulen definitionsfilen '{0}' innehåller fel. {1}|  
  

## <a name="error-0114"></a>Fel 0114  
 Ett felmeddelande när du skapar en anpassad modul misslyckas.  
  
 Det här felet i Azure Machine Learning skapas när en anpassad modul version misslyckas. Detta inträffar när en eller flera anpassade modul-relaterade fel uppstår när du lägger till anpassad modul. Ytterligare fel rapporteras i det här felmeddelandet.  
  
**Lösning:** Åtgärda fel som rapporterats inom den här Undantagsmeddelande.  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att skapa anpassad modul.|  
|Anpassad modul bygger misslyckades. fel: {0}|  
  

## <a name="error-0115"></a>Fel 0115  
 Ett felmeddelande när en anpassad modul standardskript har filnamnstillägget stöds inte.  
  
 Det här felet i Azure Machine Learning uppstår när du anger ett skript för en anpassad modul som använder ett okänt filnamnstillägg.  
  
**Lösning:** Kontrollera filnamnstillägget för format och ett filnamn för alla skriptfiler som ingår i modulen anpassade.  
  
|Undantag meddelanden|  
|------------------------|  
|-Tillägg stöds inte för standardskript.|  
|Filen stöds inte tillägget {0} för standardskript.|  
  

## <a name="error-0121"></a>Fel 0121  
 Utlöses när SQL skriver misslyckas eftersom tabellen är unwriteable  
  
 Det här felet i Azure Machine Learning skapas när du använder den [exportera Data](export-data.md) modulen att spara resultaten i en tabell i en SQL-databasen och tabellen kan inte skrivas till. Vanligtvis visas det här felet om den [exportera Data](export-data.md) modulen har upprättar en anslutning med SQL Server-instansen, men sedan kan inte skriva innehållet i Azure ML-datauppsättning till tabellen.  
  
**Lösning:**
 - Öppna fönstret Egenskaper för den [exportera Data](export-data.md) modulen och kontrollera att namnen på databasen och tabellen har angetts korrekt. 
 - Granska schemat för datauppsättningen du exporterar och se till att data är kompatibla med måltabellen.
 - Kontrollera att SQL logga in som är associerade med användarnamnet och lösenordet har behörighet att skriva till tabellen. 
 - Om undantaget innehåller ytterligare information om fel från SQL Server kan du använda informationen för att göra korrigeringar.  
  
|Undantag meddelanden|  
|------------------------|  
|Ansluten till servern, det går inte att skriva till tabellen.|  
|Det går inte att skriva till Sql-tabell: {0}|  


## <a name="error-0122"></a>Fel 0122  
 Undantaget är om flera vikt kolumner har angetts och bara en tillåts.  
  
 Det här felet i Azure Machine Learning inträffar när för många kolumner har valts som vikt kolumner.  
  
**Lösning:** Granska den inkommande datauppsättningen och dess metadata. Se till att endast en kolumn innehåller vikterna.  
  
|Undantag meddelanden|  
|------------------------|  
|Flera vikt kolumner har angetts.|  


## <a name="error-0123"></a>Fel 0123  
 Undantaget är om kolumnen vektorer har angetts till etiketten kolumn.  
  
 Det här felet i Azure Machine Learning uppstår om du använder en vector som etikettkolumnen.  
  
**Lösning:** Ändra dataformatet för kolumnen vid behov, eller välj en annan kolumn.  
  
|Undantag meddelanden|  
|------------------------|  
|Kolumn med vektorer har angetts som etikett kolumn.|  


## <a name="error-0124"></a>Fel 0124  
 Undantaget är om icke-numeriska kolumner har angetts ska kolumnen vikt.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Icke-numeriska kolumn har angetts som kolumnen vikt.|  
  


## <a name="error-0125"></a>Fel 0125  
 Ett felmeddelande när schemat för flera datauppsättningar inte överensstämmer.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Schemat för datauppsättningen matchar inte.|  


## <a name="error-0126"></a>Fel 0126  
 Undantaget är om användaren anger en SQL-domän som inte stöds i Azure ML.  
  
 Det här felet skapas när användaren anger en SQL-domän som inte stöds i Azure Machine Learning. Du får det här felet om du försöker ansluta till en databasserver i en domän som inte tillåts. Tillåtna SQL-domäner finns för närvarande ”:. database.windows.net” ”,. cloudapp.net”, eller ”. database.secure.windows.net”. Det vill säga måste servern vara en Azure SQL server eller en server i en virtuell dator på Azure.  
  
**Lösning:** Gå tillbaka till modulen. Kontrollera att SQL database-servern tillhör en av de godkända domänerna:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Undantag meddelanden|  
|------------------------|  
|SQL-domän som inte stöds.|  
|SQL-domänen {0} stöds inte för närvarande i Azure ML|  
  

## <a name="error-0127"></a>Fel 0127  
 Bild bildpunktsstorlek överskrider storleksgränsen  
  
 Det här felet uppstår om du läser bilder från en bild-datauppsättning för klassificering och bilderna är större än modellen kan hantera.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Undantag meddelanden|  
|------------------------|  
|Bild överskrider pixel storleksgränsen.|  
|Bild bildpunktsstorlek i filen ”{0}' överskrider storleksgränsen: '{1}'|  


## <a name="error-0128"></a>Fel 0128  
 Antalet villkorlig sannolikhet för kategoriska kolumner överskrider gränsen.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Antalet villkorlig sannolikhet för kategoriska kolumner överskrider gränsen.|  
|Antalet villkorlig sannolikhet för kategoriska kolumner överskrider gränsen. Kolumners{0}”och”{1}”är problematiskt paret.|  


## <a name="error-0129"></a>Fel 0129  
 Antalet kolumner i datauppsättningen överskrider storleksgränsen.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Antalet kolumner i datauppsättningen överskrider storleksgränsen.|  
|Antalet kolumner i datauppsättningen i '{0}'överskrider tillåtna ”.|  
|Antalet kolumner i datauppsättningen i ”{0}'överskrider storleksgränsen för'{1}'.'|  
|Antalet kolumner i datauppsättningen i ”{0}'överskrider det tillåtna'{1}'begränsa av'{2}'.'|  
## <a name="error-0130"></a>Fel 0130  
 Undantaget är när alla rader i en datauppsättning för träning innehåller värden som saknas.  
  
 Detta inträffar när vissa kolumn i utbildning datauppsättningen är tom.  
  
**Lösning:** Använd den [Rensa Data som saknas](clean-missing-data.md) modul för att ta bort kolumner med alla värden som saknas.  
  
|Undantag meddelanden|  
|------------------------|  
|Alla rader i datamängd för träning innehåller värden som saknas.  Överväg att använda modulen Rensa Data som saknas för att ta bort värden som saknas.|  
 

## <a name="error-0131"></a>Fel 0131  
 Undantaget är om en eller flera datauppsättningar i en zip-fil inte kan vara packat och registrerats korrekt  
  
 Det här felet skapas när en eller flera datauppsättningar i en zip-fil inte kan vara packat och läsas på rätt sätt. Det här felet visas om den uppackning misslyckas eftersom själva zipfilen eller en av filerna i den är skadad eller om det finns ett systemfel vid försök att packa upp och expandera en fil.  
  
**Lösning:** Använd informationen i felmeddelandet för att avgöra hur du fortsätter.  
  
|Undantag meddelanden|  
|------------------------|  
|Ladda upp komprimerade datauppsättningar misslyckades|  
|Zippade datauppsättning {0} misslyckades med följande meddelande: {1}|  
|Zippade datauppsättning {0} misslyckades med ett {1} undantag med meddelandet: {2}|  
  

## <a name="error-0132"></a>Fel 0132  
 Inget filnamn har angetts för uppackning; flera filer hittades i zip-filen.  
  
 Det här felet skapas när inget filnamn har angetts för uppackning; flera filer hittades i zip-filen. Du får det här felet om ZIP-filen innehåller fler än en komprimerad fil, men du har inte angett en fil för extrahering i den **datauppsättningen till uppackningen** textrutan, i den **egenskapen** rutan i modulen. För närvarande endast en fil som kan extraheras varje gång modulen körs.  
  
**Lösning:** Felmeddelandet innehåller en lista med filerna i ZIP-filen. Kopiera namnet på den önskade filen och klistra in den i den **datauppsättningen till uppackningen** textrutan.  
  
|Undantag meddelanden|  
|------------------------|  
|ZIP-filen innehåller flera filer. Du måste ange den fil som ska expanderas.|  
|Filen innehåller fler än en fil. Ange den fil som ska expanderas. Följande filer hittades: {0}|  
  

## <a name="error-0133"></a>Fel 0133  
 Gick inte att hitta den angivna filen i zip-filen  
  
 Det här felet skapas när filnamnet som angetts i den **datauppsättningen till uppackningen** i den **egenskapen** fönstret matchar inte namnet på alla filer i ZIP-filen. De vanligaste orsakerna till felet är att skriva fel eller söka fel arkivfilen för filen för att expandera.  
  
**Lösning:** Gå tillbaka till modulen. Om namnet på filen som du försöker att dekomprimera visas i listan över filer som hittades, kopierar du filen namnet och klistra in den i den **datauppsättningen till uppackningen** egenskapsrutan. Om du inte ser önskat filnamn i listan, kan du kontrollera att du har rätt .zip-fil och önskad fil rätt namn.  
  
|Undantag meddelanden|  
|------------------------|  
|Den angivna filen gick inte att hitta int zip-filen.|  
|Det gick inte att hitta den angivna filen. Hitta följande filer: {0}|  
  

## <a name="error-0134"></a>Fel 0134
Undantaget är när Etikettkolumnen saknas eller har tillräckligt många taggade rader.  
  
Det här felet uppstår när modulen kräver en etikett-kolumn, men du inte angav en i kolumnen eller Etikettkolumnen saknas för många värden.

Det här felet kan också inträffa när en tidigare åtgärd ändras datauppsättningen, så att inte tillräckligt med rader som är tillgängliga för en underordnad åtgärd. Anta exempelvis att du använder ett uttryck i den **partitionera och ta prover** modulen att dela en datauppsättning med värden. Om inga matchningar hittades för uttrycket, är en av de datauppsättningar som härrör från partitionen tom.

Lösning: 

 Om du inkluderar en etikett-kolumn i kolumnen, men det inte känns igen, använda den [redigera Metadata](edit-metadata.md) modul för att markera det som en etikett-kolumn.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Du kan sedan använda den [Rensa Data som saknas](clean-missing-data.md) modulen att ta bort rader med värden som saknas i etikettkolumnen. 

 Kontrollera dina datauppsättningar som indata för att se till att de innehåller giltiga data och tillräckligt med rader som ska uppfylla kraven för åtgärden. Många algoritmer genererar ett felmeddelande om de kräver vissa minsta antal rader med data, men data innehåller bara några rader eller bara en rubrik.
  
|Undantag meddelanden|
|------------------------|
|Undantaget är när Etikettkolumnen saknas eller har tillräckligt många taggade rader.|  
|Undantaget är när etiketten kolumn saknas eller är mindre än {0} märkta rader|  
  

## <a name="error-0135"></a>Fel 0135  
 Endast axelcentroid-baserat kluster stöds.  
  
**Lösning:** Du kan stöta på det här felmeddelandet om du har försökt att utvärdera en klustringsmodell som baseras på en anpassad klustring algoritm som inte använder centroids för att initiera klustret.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Undantag meddelanden|  
|------------------------|  
|Endast axelcentroid-baserat kluster stöds.|  
  

## <a name="error-0136"></a>Fel 0136  
 Inget filnamn returnerades; Det går inte att bearbeta filen som ett resultat.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Inget filnamn returnerades; Det går inte att bearbeta filen som ett resultat.|  
  

## <a name="error-0137"></a>Fel 0137  
 Azure Storage SDK påträffade ett fel som konvertering mellan Tabellegenskaper och kolumner för datauppsättningar under läsning eller skrivning.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Konverteringsfel mellan Azure storage egenskap och datauppsättningen tabellkolumn.|  
|Konverteringsfel mellan Azure storage egenskap och datauppsättningen tabellkolumn. Ytterligare information: {0}|  

## <a name="error-0138"></a>Fel 0138  
 Minne har uppnåtts kan inte slutförd körning av modulen. Nedsampling datauppsättningen hjälpa dig för att lösa problemet.  
  
 Det här felet uppstår när den modul som körs kräver mer minne än vad som är tillgängliga i Azure-behållaren. Detta kan inträffa om du arbetar med en stor datauppsättning och den aktuella åtgärden inte får plats i minnet.  
  
**Lösning:** Om du försöker läsa en stor datauppsättning och går inte att slutföra åtgärden, nedsampling datauppsättningen leda till.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Undantag meddelanden|  
|------------------------|  
|Minne har uppnåtts kan inte slutförd körning av modulen.|  
  

## <a name="error-0139"></a>Fel 0139  
 Undantaget är när det inte går att konvertera en kolumn till en annan typ.  
  
 Det här felet i Azure Machine Learning uppstår när du försöker konvertera en kolumn till en annan datatyp, men som typen stöds inte av den aktuella åtgärden eller av modulen.  
  
 Felet kan också visas när en modul försöker att implicit konvertera data för att uppfylla kraven för den aktuella modulen, men konverteringen inte är möjligt.  
  
**Lösning:**

1. Granska dina indata och kontrollera exakt datatypen för kolumnen som du vill använda och datatypen för kolumnen som ger felet. Ibland överväga du bör att datatypen är korrekt, men hitta att en överordnad åtgärd har ändrat datatypen eller användning av en kolumn. Använd den [redigera Metadata](edit-metadata.md) modul för att återställa kolumnmetadata till det ursprungliga tillståndet. 
2. Ta en titt på hjälpsidan för modulen för att kontrollera kraven för den angivna åtgärden. Avgöra vilka datatyper som stöds av den aktuella modulen och vilka värdeintervallet stöds. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Överväg om det är möjligt att konvertera eller konvertera kolumnen till en annan datatyp. Alla följande moduler ger stor flexibilitet och power för att ändra data: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Köra Python-skriptet](execute-python-script.md).  

> [!NOTE]
> Fortfarande inte fungerar? Överväg att tillhandahålla ytterligare feedback på problemet som hjälper oss att utveckla bättre felsökningsinformation. Bara skicka feedback om den här sidan och ange namnet på den modul som genererade felet och konverteringen av datatyp som misslyckades.
  
|Undantag meddelanden|  
|------------------------|  
|Inte tillåtet att konverteringen.|  
|Det gick inte att konvertera: {0}.|  
|Det gick inte att konvertera: {0}, på rad {1}.|  
|Det gick inte att konvertera en kolumn av typen {0} till kolumn av typen {1} på rad {2}.|  
|Det gick inte att konvertera kolumnen ”{2}” av typen {0} till kolumn av typen {1} på rad {3}.|  
|Det gick inte att konvertera kolumnen ”{2}” av typen {0} till kolumn ”{3}” av typen {1} på rad {4}.| 

## <a name="error-0140"></a>Fel 0140  
 Undantaget är om du kolumnen set argumentet inte innehåller andra kolumner utom etikett kolumn.  
  
 Det här felet uppstår om du har anslutit en datauppsättning till en modul som kräver flera kolumner, inklusive funktioner, men du har angett bara etikettkolumnen.  
  
**Lösning:** Välj minst en kolumn för funktionen ska ingå i datauppsättningen.  
  
|Undantag meddelanden|  
|------------------------|  
|Angivna kolumnuppsättning innehåller inte andra kolumner utom etikett kolumn.|  
  

## <a name="error-0141"></a>Fel 0141  
 Undantaget är om antalet valda numeriska kolumner och unika värden i den kategoriska och strängkolumner är för liten.  
  
 Det här felet i Azure Machine Learning inträffar när det inte finns tillräckligt med unika värden i den markerade kolumnen att utföra åtgärden.  
  
**Lösning:** Vissa åtgärder genomför statistiska åtgärder i funktionen och kategoriska kolumner och om det inte finns tillräckligt med värden, det kan misslyckas eller returnera ett ogiltigt resultat. Kontrollera din datauppsättning för att se hur många värden som finns i kolumnerna funktionen och etiketten och avgöra om du försöker utföra åtgärden är statistiskt.  
  
 Om datauppsättningen för källan är giltig, kan du också kontrollera om överordnad manipulering eller metadata åtgärder har ändrats data och ta bort vissa värden.  
  
 Om överordnade åtgärder är bland annat dela sampling eller sampla, kontrollerar du att utdata innehåller det förväntade antalet rader och värden.  
  
|Undantag meddelanden|  
|------------------------|  
|Antalet valda numeriska kolumner och unika värden i den kategoriska och strängkolumner är för liten.|  
|Det totala antalet valda numeriska kolumner och unika värden i den kategoriska och strängkolumner (för närvarande {0}) bör vara minst {1}|  
  

## <a name="error-0142"></a>Fel 0142  
 Undantaget är när systemet inte kan läsa in certifikat för att autentisera.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Certifikatet kan inte läsas in.|  
|Certifikatet {0} kan inte läsas in. Dess tumavtrycket är {1}.|  
  

## <a name="error-0143"></a>Fel 0143  
 Det går inte att parsa URL för användaren som ska vara från GitHub.  
  
 Det här felet i Azure Machine Learning uppstår när du anger en ogiltig URL och modulen kräver en giltig URL i GitHub.  
  
**Lösning:** Kontrollera att URL: en refererar till en giltig GitHub-lagringsplats. Andra platstyper stöds inte.  
  
|Undantag meddelanden|  
|------------------------|  
|Webbadressen är inte från github.com.|  
|URL: en är inte från github.com: {0}|  

## <a name="error-0144"></a>Fel 0144  
 Användardefinierade GitHub-url saknas i den förväntade delen.  
  
 Det här felet i Azure Machine Learning uppstår när du anger en GitHub-filkälla med en ogiltig URL-formatet.  
  
**Lösning:** Kontrollera att URL: en för GitHub-lagringsplatsen är giltig och slutar med \blob\ eller \tree\\.  
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att parsa URL: en för GitHub.|  
|Det går inte att parsa URL: en för GitHub (förväntas ' \blob\\' eller ' \tree\\' efter namnet på lagringsplatsen): {0}|  

## <a name="error-0145"></a>Fel 0145  
 Det går inte att skapa katalogen replikering av någon anledning.  
  
 Det här felet i Azure Machine Learning uppstår när modulen inte att skapa den angivna katalogen.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att skapa katalog för replikering.|  
  

## <a name="error-0146"></a>Fel 0146  
 När användarfiler är uppzippade till den lokala katalogen kan kan den kombinerade sökvägen vara för lång.  
  
 Det här felet i Azure Machine Learning inträffar när du extraherar filer men vissa filnamn är för långa när packat.  
  
**Lösning:** Redigera filnamnen så att kombineras sökvägen och filnamnet är längre än 248 tecken.  
  
|Undantag meddelanden|  
|------------------------|  
|Replikeringssökvägen är längre än 248 tecken, förkorta skriptets namn eller sökvägen.|  

## <a name="error-0147"></a>Fel 0147  
 Kunde inte ladda ned innehåll från GitHub av någon anledning  
  
 Det här felet i Azure Machine Learning inträffar när du inte kan läsa eller ladda ned de angivna filerna från GitHub.  
  
**Lösning:** Problemet kan vara tillfälliga; Du kan försöka komma åt filerna vid ett senare tillfälle. Eller kontrollera att du har behörighet och att källan är giltig.  
  
|Undantag meddelanden|  
|------------------------|  
|GitHub-åtkomstfel.|  
|GitHub-åtkomstfel. {0}|  
  

## <a name="error-0148"></a>Fel 0148  
 Obehörig åtkomstproblem vid extrahering av data eller skapar katalog.  
  
 Det här felet i Azure Machine Learning uppstår när du försöker skapa en katalog eller läsa data från lagring men har inte tillräcklig behörighet.  
  
**Lösning:**
  
|Undantag meddelanden|  
|------------------------|  
|Undantag för obehörig åtkomst vid extrahering av data.|  
  

## <a name="error-0149"></a>Fel 0149  
 Användare-filen finns inte i GitHub-paket.  
  
 Det här felet i Azure Machine Learning inträffar när det går inte att hitta den angivna filen.  
  
Lösning: 
  
|Undantag meddelanden|  
|------------------------|  
|GitHub-filen hittades inte.|  
|GitHub-filen hittades inte.: {0}|  
  

## <a name="error-0150"></a>Fel 0150  
 Skripten som kommer från användar-paketet kunde inte uppzippade, troligen på grund av en kollision med GitHub-filer.  
  
 Det här felet i Azure Machine Learning inträffar när ett skript inte kan extraheras, vanligtvis när det finns en befintlig fil med samma namn.  
  
Lösning:
  
|Undantag meddelanden|  
|------------------------|  
|Det går inte att packa upp paketet; möjliga namnet står i konflikt med GitHub-filer.|  
  

## <a name="error-0151"></a>Fel 0151  
 Det uppstod ett fel vid skrivning till molnlagring. Kontrollera URL: en.  
  
 Det här felet i Azure Machine Learning inträffar när modulen försöker skriva data till lagring i molnet men Webbadressen är inte tillgänglig eller är ogiltig.  
  
Lösning: Kontrollera URL: en och kontrollera att den är skrivbar.  
  
|Undantag meddelanden|  
|------------------------|  
|Fel vid skrivning till molnlagring (eventuellt en felaktig url).|  
|Fel vid skrivning till molnlagring: {0}. Kontrollera URL: en.|  
  
## <a name="error-0152"></a>Fel 0152  
 Azure cloud-typ angavs felaktigt i modulen kontexten.  
  
|Undantag meddelanden|  
|------------------------|  
|Felaktig Azure Cloud-typ|  
|Felaktig Azure Cloud-typ: {0}|  
  
## <a name="error-0153"></a>Fel 0153  
 Storage-slutpunkt som angetts är ogiltig.  
  
|Undantag meddelanden|  
|------------------------|  
|Felaktig Azure Cloud-typ|  
|Felaktig Storage-slutpunkt: {0}|  

## <a name="error-0154"></a>Fel 0154  
 Det gick inte att matcha det angivna servernamnet  
  
|Undantag meddelanden|  
|------------------------|  
|Det gick inte att matcha det angivna servernamnet|  
|Den angivna servern {0}. documents.azure.com gick inte att matcha|

## <a name="error-0155"></a>Fel 0155  
 DocDb-Client kastade ett undantag  
  
|Undantag meddelanden|  
|------------------------|  
|DocDb-Client kastade ett undantag|  
|DocDb Client: {0}|

## <a name="error-0156"></a>Fel 0156  
 Felaktigt svar för HCatalog Server.  
  
|Undantag meddelanden|  
|------------------------|  
|Felaktigt svar för HCatalog Server. Kontrollera att alla tjänster körs.|  
|Felaktigt svar för HCatalog Server. Kontrollera att alla tjänster körs. Felinformation: {0}|

## <a name="error-0157"></a>Fel 0157  
 Det gick inte att läsa från Azure Cosmos DB på grund av inkonsekvent eller andra dokumentscheman. Läsare kräver att alla dokument har samma schema.  
  
|Undantag meddelanden|  
|------------------------|  
|Identifierade dokument med olika scheman. Kontrollera att alla dokument har samma schema|

## <a name="error-1000"></a>Fel 1000  
Internt bibliotek för undantag.  
  
Det här felet har angetts för att samla in annars ohanterat motor för internt fel. Orsak till det här felet kan därför vara olika beroende på den modul som gav upphov till felet.  
  
Om du behöver mer hjälp, rekommenderar vi att du lägger upp detaljerat meddelande som medföljer felet på Azure Machine Learning-forumet, tillsammans med en beskrivning av scenariot med de data som används som indata. Denna feedback hjälper oss att prioritera fel och identifiera de viktigaste frågorna för ytterligare arbete.  
  
|Undantag meddelanden|  
|------------------------|  
|Undantag i biblioteket.|  
|Biblioteket undantag: {0}|  
|{0} biblioteket undantag: {1}|  
