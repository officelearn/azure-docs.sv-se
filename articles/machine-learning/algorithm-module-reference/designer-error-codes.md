---
title: Felsöka modulfel
titleSuffix: Azure Machine Learning
description: Felsöka modulundantag i Azure Machine Learning designer med hjälp av felkoder
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: cc04d11475568af92ba6a617a1eb6b2b51accb45
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481673"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Undantag och felkoder för designern (förhandsgranskning)

I den här artikeln beskrivs felmeddelanden och undantagskoder i Azure Machine Learning Designer (förhandsversion) som hjälper dig att felsöka pipelines för maskininlärning.

Du hittar felmeddelandet i designern följande steg:  

- Välj den misslyckade modulen, gå till fliken **Utdata+loggar,** du hittar den detaljerade loggen i **filen 70_driver_log.txt** under kategorin **azureml-logs.**

- För detaljerad modulfel kan du kontrollera det i error_info.json under **module_statistics** kategori.

Följande är felkoder för moduler i designern.

## <a name="error-0001"></a>Fel 0001  
 Undantag inträffar om det inte gick att hitta en eller flera angivna kolumner med datauppsättningen.  

 Det här felet visas om en kolumnval görs för en modul, men de valda kolumnerna inte finns i indatauppsättningen. Det här felet kan uppstå om du manuellt har skrivit in ett kolumnnamn eller om kolumnväljaren har angett en föreslagen kolumn som inte fanns i datauppsättningen när du körde pipelinen.  

**Upplösning:** Gå tillbaka till modulen som kastar det här undantaget och verifiera att kolumnnamnet eller namnen är korrekta och att alla refererade kolumner finns.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att hitta en eller flera angivna kolumner.|
|Det gick inte att hitta en kolumn med namnet eller indexet {column_id}.|
|Kolumnen med namnet eller indexet {column_id}finns inte i {arg_name_missing_column}.|
|Kolumnen med namnet eller indexet {column_id}finns inte i {arg_name_missing_column}, men finns i {arg_name_has_column}.|
|Det gick inte att hitta kolumner med namn eller index {column_names}.|
|Kolumner med namnet eller indexet {column_names}finns inte i {arg_name_missing_column}.|
|Kolumner med namnet eller indexet {column_names}finns inte i {arg_name_missing_column}, men finns i {arg_name_has_column}.|


## <a name="error-0002"></a>Fel 0002  
 Undantag inträffar om en eller flera parametrar inte kunde tolkas eller konverteras från angiven typ till obligatorisk metodtyp.  

 Det här felet uppstår i Azure Machine Learning när du anger en parameter som indata och värdetypen skiljer sig från den typ som förväntas och implicit konvertering kan inte utföras.  

**Upplösning:** Kontrollera modulkraven och bestäm vilken värdetyp som krävs (sträng, heltal, dubbel, etc.)  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att tolka parametern.|
|Det gick inte att tolka parametern {arg_name_or_column}.|
|Det gick inte att konvertera parametern {arg_name_or_column}till {to_type}.|
|Det gick inte att konvertera parametern {arg_name_or_column}" från {from_type}" till {to_type}.|
|Det gick inte att konvertera parametervärdet {arg_name_or_column}, "{arg_value}" från {from_type}" till {to_type}.|
|Det gick inte att konvertera värdet {arg_value}" i kolumnen {arg_name_or_column}" från {from_type}" till {to_type}" med användningen av formatet {fmt}.|


## <a name="error-0003"></a>Fel 0003  
 Undantag inträffar om en eller flera indata är null eller tomma.  

 Det här felet visas i Azure Machine Learning om några indata eller parametrar till en modul är null eller tomma.  Det här felet kan uppstå, till exempel när du inte skrev in något värde för en parameter. Det kan också hända om du väljer en datauppsättning som saknar värden eller en tom datauppsättning.  

**Upplösning:**

+ Öppna modulen som producerade undantaget och kontrollera att alla indata har angetts. Kontrollera att alla nödvändiga indata har angetts. 
+ Kontrollera att data som läses in från Azure-lagring är tillgängliga och att kontonamnet eller nyckeln inte har ändrats.  
+ Kontrollera indata för saknade värden eller null.
+ Om du använder en fråga på en datakälla kontrollerar du att data returneras i det format du förväntar dig. 
+ Kontrollera om det finns stavfel eller andra ändringar i dataspecifikationen.
  
|Undantagsmeddelanden|
|------------------------|
|En eller flera indata är null eller tomma.|
|Indata {name}är null eller tom.|


## <a name="error-0004"></a>Fel 0004  
 Undantag inträffar om parametern är mindre än eller lika med det specifika värdet.  

 Det här felet visas i Azure Machine Learning om parametern i meddelandet ligger under ett gränsvärde som krävs för att modulen ska kunna bearbeta data.  

**Upplösning:** Gå tillbaka till modulen som kastar undantaget och ändra parametern så att den är större än det angivna värdet.  

|Undantagsmeddelanden|
|------------------------|
|Parametern ska vara större än gränsvärdet.|
|Parametern {arg_name}" värdet ska vara större än {lower_boundary}.|
|Parametern {arg_name}" har värdet {actual_value}, som ska vara större än {lower_boundary}.|


## <a name="error-0005"></a>Fel 0005  
 Undantag inträffar om parametern är mindre än ett visst värde.  

 Det här felet visas i Azure Machine Learning om parametern i meddelandet är lägre än eller lika med ett gränsvärde som krävs för att modulen ska kunna bearbeta data.  

**Upplösning:** Se över modulen som kastar undantaget och ändra parametern så att den är större än eller lika med det angivna värdet.  

|Undantagsmeddelanden|
|------------------------|
|Parametern ska vara större än eller lika med gränsvärdet.|
|Parametern {arg_name}" ska vara större än eller lika med {lower_boundary}.|
|Parametern {arg_name}" har värdet {value}" som ska vara större än eller lika med {lower_boundary}.|


## <a name="error-0006"></a>Fel 0006  
 Undantag inträffar om parametern är större än eller lika med det angivna värdet.  

 Det här felet visas i Azure Machine Learning om parametern i meddelandet är större än eller lika med ett gränsvärde som krävs för att modulen ska kunna bearbeta data.  

**Upplösning:** Gå tillbaka till modulen som kastar undantaget och ändra parametern så att den är mindre än det angivna värdet.  

|Undantagsmeddelanden|
|------------------------|
|Parametrar matchar inte. En av parametrarna bör vara mindre än en annan.|
|Parametern {arg_name}" värdet ska vara mindre än parametern {upper_boundary_parameter_name}-värde.|
|Parametern {arg_name}" har värdet {value}, som ska vara mindre än {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Fel 0007  
 Undantag inträffar om parametern är större än ett visst värde.  

 Det här felet visas i Azure Machine Learning om du i egenskaperna för modulen har angett ett värde som är större än vad som är tillåtet. Du kan till exempel ange data som ligger utanför intervallet för datum som stöds, eller så kan du ange att fem kolumner ska användas när endast tre kolumner är tillgängliga. 

 Det här felet kan också visas om du anger två uppsättningar data som måste matchas på något sätt. Om du till exempel byter namn på kolumner och anger kolumnerna efter index måste antalet namn som du anger matcha antalet kolumnindex. Ett annat exempel kan vara en matematisk åtgärd som använder två kolumner, där kolumnerna måste ha samma antal rader. 

**Upplösning:**

 + Öppna modulen i fråga och granska eventuella numeriska egenskapsinställningar.
 + Kontrollera att alla parametervärden ligger inom det värdeintervall som stöds för den egenskapen.
 + Om modulen tar flera ingångar, se till att ingångarna är av samma storlek.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Kontrollera om datauppsättningen eller datakällan har ändrats. Ibland misslyckas ett värde som fungerade med en tidigare version av data efter att antalet kolumner, kolumndatatyperna eller storleken på data har ändrats.  

|Undantagsmeddelanden|
|------------------------|
|Parametrar matchar inte. En av parametrarna ska vara mindre än eller lika med en annan.|
|Parametern {arg_name}"-värdet ska vara mindre än eller lika med parametern {upper_boundary_parameter_name}-värde.|
|Parametern {arg_name}" har värdet {{actual_value}" som ska vara mindre än eller lika med {upper_boundary}.|
|Parametern {arg_name}" {actual_value} ska vara mindre än eller lika med parametern {upper_boundary_parameter_name}- värde {upper_boundary}.|


## <a name="error-0008"></a>Fel 0008  
 Undantag inträffar om parametern inte är i intervallet.  

 Det här felet visas i Azure Machine Learning om parametern i meddelandet ligger utanför de gränser som krävs för att modulen ska kunna bearbeta data.  

 Det här felet visas till exempel om du försöker använda Lägg till [rader](add-rows.md) för att kombinera två datauppsättningar som har ett annat antal kolumner.  

**Upplösning:** Se över modulen som kastar undantaget och ändra parametern så att den ligger inom det angivna intervallet.  

|Undantagsmeddelanden|
|------------------------|
|Parametervärdet finns inte i det angivna intervallet.|
|Parametern {arg_name}" är inte i intervallet.|
|Parametern {arg_name}" ska ligga i intervallet [{lower_boundary}, {upper_boundary}].|
|Parametern {arg_name}" är inte i intervallet. {orsak}|


## <a name="error-0009"></a>Fel 0009  
 Undantaget inträffar när Azure-lagringskontonamnet eller behållarnamnet anges felaktigt.  

Det här felet uppstår i Azure Machine Learning designer när du anger parametrar för ett Azure-lagringskonto, men namnet eller lösenordet kan inte matchas. Fel på lösenord eller kontonamn kan uppstå av många skäl:

 + Kontot är fel typ. Vissa nya kontotyper stöds inte för användning med Machine Learning-designer. Mer information finns i [Importera data.](import-data.md)
 + Du angav det felaktiga kontonamnet
 + Kontot finns inte längre
 + Lösenordet för lagringskontot är felaktigt eller har ändrats
 + Du har inte angett behållarnamnet eller så finns inte behållaren
 + Du har inte angett filsökvägen helt (sökvägen till blobben)
   

**Upplösning:**

Sådana problem uppstår ofta när du försöker ange kontonamn, lösenord eller behållarsökväg manuellt. Vi rekommenderar att du använder den nya guiden för modulen [Importera data,](import-data.md) som hjälper dig att slå upp och kontrollera namn.

Kontrollera också om kontot, behållaren eller blobben har tagits bort. Använd ett annat Azure-lagringsverktyg för att verifiera att kontonamnet och lösenordet har angetts korrekt och att behållaren finns. 

Vissa nyare kontotyper stöds inte av Azure Machine Learning. De nya lagringstyperna "hot" eller "cold" kan till exempel inte användas för maskininlärning. Både klassiska lagringskonton och lagringskonton som skapats som "Allmänt ändamål" fungerar bra.

Om den fullständiga sökvägen till en blob angavs kontrollerar du att sökvägen har angetts som **behållare/blobnamn**och att både behållaren och blobben finns i kontot.  

 Sökvägen ska inte innehålla ett inledande snedstreck. Till exempel **/container/blob** är felaktig och bör anges som **behållare/blob**.  


|Undantagsmeddelanden|
|------------------------|
|Azure-lagringskontonamnet eller behållarnamnet är felaktigt.|
|Azure-lagringskontonamnet {account_name}" eller behållarnamnet {container_name}är felaktigt. ett behållarnamn för formatbehållaren/bloben förväntades.|


## <a name="error-0010"></a>Fel 0010  
 Undantag inträffar om indatauppsättningar har kolumnnamn som ska matcha men inte.  

 Det här felet visas i Azure Machine Learning om kolumnindexet i meddelandet har olika kolumnnamn i de två indatauppsättningarna.  

**Upplösning:** Använd [Redigera metadata](edit-metadata.md) eller ändra den ursprungliga datauppsättningen så att den har samma kolumnnamn för det angivna kolumnindexet.  

|Undantagsmeddelanden|
|------------------------|
|Kolumner med motsvarande index i indatauppsättningar har olika namn.|
|Kolumnnamn är inte desamma för kolumn {col_index} (nollbaserade) av indatauppsättningar ({dataset1} respektive {dataset2}).|


## <a name="error-0011"></a>Fel 0011  
 Undantag inträffar om det skickade kolumnuppsättningsargumentet inte gäller för någon av datauppsättningskolumnerna.  

 Det här felet visas i Azure Machine Learning om det angivna kolumnvalet inte matchar någon av kolumnerna i den angivna datauppsättningen.  

 Du kan också få det här felet om du inte har valt en kolumn och minst en kolumn krävs för att modulen ska fungera.  

**Upplösning:** Ändra kolumnmarkeringen i modulen så att den tillämpas på kolumnerna i datauppsättningen.  

 Om modulen kräver att du väljer en viss kolumn, till exempel en etikettkolumn, kontrollerar du att den högra kolumnen är markerad.  

 Om olämpliga kolumner är markerade tar du bort dem och kör pipelinen igen.  

|Undantagsmeddelanden|
|------------------------|
|Den angivna kolumnuppsättningen gäller inte för någon av datauppsättningskolumnerna.|
|Den angivna kolumnuppsättningen {column_set}" gäller inte för någon av datamängdskolumnerna.|


## <a name="error-0012"></a>Fel 0012  
 Undantag inträffar om instansen av klassen inte kunde skapas med skickade argument.  

**Upplösning:** Det här felet kan inte användas av användaren och kommer att inaktuella i en framtida version.  

|Undantagsmeddelanden|
|------------------------|
|Otränad modell, vänligen träna modell först.|
|Otränad modell ({arg_name}), använd tränad modell.|


## <a name="error-0013"></a>Fel 0013  
 Undantag inträffar om eleven som skickas till modulen är en ogiltig typ.  

 Det här felet uppstår när en tränad modell är inkompatibel med den anslutna poängsättningsmodulen. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Upplösning:**

Bestäm vilken typ av elev som produceras av utbildningsmodulen och bestäm den bedömningsmodul som är lämplig för eleven. 

Om modellen har tränats med någon av de specialiserade utbildningsmodulerna ansluter du endast den tränade modellen till motsvarande specialiserade poängsättningsmodul. 


|Typ av modell|Utbildningsmodul| Poängsättningsmodul|
|----|----|----|
|alla klassificerare|[Träningsmodell](train-model.md) |[Poängmodell](score-model.md)|
|någon regressionsmodell|[Träningsmodell](train-model.md) |[Poängmodell](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Undantagsmeddelanden|
|------------------------|
|Eleven av ogiltig typ skickas.|
|Eleven {arg_name}har ogiltig typ.|
|Eleven {arg_name}" har ogiltig typ {learner_type}.|


## <a name="error-0014"></a>Fel 0014  
 Undantag inträffar om antalet unika kolumnvärden är större än tillåtet.  

 Det här felet uppstår när en kolumn innehåller för många unika värden.  Det här felet kan till exempel visas om du anger att en kolumn ska hanteras som kategoriska data, men det finns för många unika värden i kolumnen för att tillåta bearbetningen att slutföras. Du kan också se det här felet om det finns en obalans mellan antalet unika värden i två indata.   

**Upplösning:**

Öppna modulen som genererade felet och identifiera de kolumner som används som indata. För vissa moduler kan du högerklicka på datauppsättningsindata och välja **Visualisera** för att hämta statistik om enskilda kolumner, inklusive antalet unika värden och deras distribution.

För kolumner som du tänker använda för gruppering eller kategorisering vidtar du åtgärder för att minska antalet unika värden i kolumner. Du kan minska på olika sätt, beroende på kolumnens datatyp. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Det går inte att hitta en lösning som matchar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på modulen som genererade felet och datatypen och kardinaliteten i kolumnen. Vi använder informationen för att tillhandahålla mer riktade felsökningssteg för vanliga scenarier.   

|Undantagsmeddelanden|
|------------------------|
|Mängden kolumn unika värden är större än tillåtet.|
|Antalet unika värden i kolumnen: "{column_name}" är större än tillåtet.|
|Antalet unika värden i kolumnen: "{column_name}" överskrider tuppelantalet {limitation}.|


## <a name="error-0015"></a>Fel 0015  
 Undantag inträffar om databasanslutningen har misslyckats.  

 Det här felet visas om du anger ett felaktigt SQL-kontonamn, lösenord, databasserver eller databasnamn, eller om en anslutning till databasen inte kan upprättas på grund av problem med databasen eller servern.  

**Upplösning:** Kontrollera att kontonamn, lösenord, databasserver och databas har angetts korrekt och att det angivna kontot har rätt behörighetsnivå. Kontrollera att databasen är tillgänglig för tillfället.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att upprätta databasanslutning.|
|Det gick inte att upprätta databasanslutningen: {connection_str}.|


## <a name="error-0016"></a>Fel 0016  
 Undantag inträffar om indatauppsättningar som skickas till modulen ska ha kompatibla kolumntyper men inte.  

 Det här felet visas i Azure Machine Learning om de typer av kolumner som skickas i två eller flera datauppsättningar inte är kompatibla med varandra.  

**Upplösning:** Använda [Redigera metadata](edit-metadata.md) eller ändra den ursprungliga indatauppsättningen<!--, or use [Convert to Dataset](convert-to-dataset.md)--> för att säkerställa att typerna av kolumnerna är kompatibla.  

|Undantagsmeddelanden|
|------------------------|
|Kolumner med motsvarande index i indatauppsättningar har inkompatibla typer.|
|Kolumnerna {first_col_names} är inkompatibla mellan tåg- och testdata.|
|Kolumnerna {first_col_names} och {second_col_names} är inkompatibla.|
|Kolumnelementtyper är inte kompatibla för kolumnen {first_col_names}( nollbaserad) av indatauppsättningar ({first_dataset_names} respektive {second_dataset_names}).|


## <a name="error-0017"></a>Fel 0017  
 Undantag inträffar om en markerad kolumn använder en datatyp som inte stöds av den aktuella modulen.  

 Det här felet kan till exempel visas i Azure Machine Learning om kolumnvalet innehåller en kolumn med en datatyp som inte kan bearbetas av modulen, till exempel en strängkolumn för en matematisk åtgärd eller en poängkolumn där en kategoriskönskumn krävs.  

**Upplösning:**
 1. Identifiera den kolumn som är problemet.
 2. Granska kraven för modulen.
 3. Ändra kolumnen så att den uppfyller kraven. Du kan behöva använda flera av följande moduler för att göra ändringar, beroende på kolumnen och konverteringen du försöker:
    + Använd [Redigera metadata](edit-metadata.md) för att ändra datatypen för kolumner eller för att ändra kolumnanvändningen från funktionen till numerisk, kategorisk till icke-kategorisk och så vidare.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Som en sista utväg kan du behöva ändra den ursprungliga indatauppsättningen.

> [!TIP]
> Det går inte att hitta en lösning som matchar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på modulen som genererade felet och datatypen och kardinaliteten i kolumnen. Vi använder informationen för att tillhandahålla mer riktade felsökningssteg för vanliga scenarier. 

|Undantagsmeddelanden|
|------------------------|
|Det går inte att bearbeta kolumnen av aktuell typ. Typen stöds inte av modulen.|
|Det går inte att bearbeta kolumnen av typen {col_type}. Typen stöds inte av modulen.|
|Det går inte att bearbeta kolumnen {col_name}" av typen {col_type}. Typen stöds inte av modulen.|
|Det går inte att bearbeta kolumnen {col_name}" av typen {col_type}. Typen stöds inte av modulen. Parameternamn: {arg_name}.|


## <a name="error-0018"></a>Fel 0018  
 Undantag inträffar om indatauppsättningen inte är giltig.  

**Upplösning:** Det här felet i Azure Machine Learning kan visas i många sammanhang, så det finns inte en enda lösning. I allmänhet anger felet att de data som lämnas som indata till en modul har fel antal kolumner eller att datatypen inte matchar modulens krav. Ett exempel:  

-   Modulen kräver en etikettkolumn, men ingen kolumn är markerad som en etikett eller så har du inte markerat en etikettkolumn ännu.  
  
-   Modulen kräver att data ska vara kategoriska men dina data är numeriska.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Data är i fel format.  
  
-   Importerade data innehåller ogiltiga tecken, felaktiga värden eller utanför intervallvärden.  
-   Kolumnen är tom eller innehåller för många värden som saknas.  

 Om du vill ta reda på kraven och hur dina data kan granska hjälpavsnittet för modulen som ska förbruka datauppsättningen som indata.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Undantagsmeddelanden|
|------------------------|
|Datauppsättningen är ogiltig.|
|{dataset1} innehåller ogiltiga data.|
|{dataset1} och {dataset2} ska vara konsekventa i kolumnikerna.|
|{dataset1} innehåller ogiltiga data, {reason}.|
|{dataset1} innehåller {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} är ogiltigt, {orsak}. {troubleshoot_hint}|


## <a name="error-0019"></a>Fel 0019  
 Undantag inträffar om kolumnen förväntas innehålla sorterade värden, men det gör det inte.  

 Det här felet visas i Azure Machine Learning om de angivna kolumnvärdena är ur funktion.  

**Upplösning:** Sortera kolumnvärdena genom att manuellt ändra indatauppsättningen och köra modulen igen.  

|Undantagsmeddelanden|
|------------------------|
|Värden i kolumnen sorteras inte.|
|Värden i kolumnen {col_index}} sorteras inte.|
|Värden i kolumnen {col_index}" av datauppsättningen {dataset} sorteras inte.|


## <a name="error-0020"></a>Fel 0020  
 Undantag inträffar om antalet kolumner i vissa datauppsättningar som skickas till modulen är för litet.  

 Det här felet visas i Azure Machine Learning om inte tillräckligt många kolumner har valts för en modul.  

**Upplösning:** Se till att kolumnväljaren har markerat rätt antal kolumner.  

|Undantagsmeddelanden|
|------------------------|
|Antalet kolumner i indatauppsättningen är mindre än tillåtet minimum.|
|Antalet kolumner i indatauppsättningen {arg_name}är mindre än tillåtet minimum.|
|Antalet kolumner i indatauppsättningen är mindre än tillåtet minimum av {required_columns_count} kolumner.|
|Antalet kolumner i indatauppsättningen {arg_name}" är mindre än tillåtet minimum av {required_columns_count} kolumner.|


## <a name="error-0021"></a>Fel 0021  
 Undantag inträffar om antalet rader i vissa datauppsättningar som skickas till modulen är för litet.  

 Det här felet i sett i Azure Machine Learning när det inte finns tillräckligt med rader i datauppsättningen för att utföra den angivna åtgärden. Det här felet kan till exempel visas om indatauppsättningen är tom eller om du försöker utföra en åtgärd som kräver att ett minsta antal rader är giltiga. Sådana åtgärder kan omfatta (men är inte begränsade till) gruppering eller klassificering baserat på statistiska metoder, vissa typer av binning och inlärning med antal.  

**Upplösning:**

 + Öppna modulen som returnerade felet och kontrollera indatauppsättningen och modulegenskaperna. 
 + Kontrollera att indatauppsättningen inte är tom och att det finns tillräckligt med rader med data för att uppfylla kraven som beskrivs i modulhjälpen.  
 + Om dina data läses in från en extern källa kontrollerar du att datakällan är tillgänglig och att det inte finns något fel eller någon ändring i datadefinitionen som gör att importprocessen får färre rader.
 + Om du utför en åtgärd på datauppströmmen för modulen som kan påverka typen av data eller antalet värden, till exempel rensning, delning eller kopplingsåtgärder, kontrollerar du utdata för dessa åtgärder för att fastställa antalet rader som returneras.  

|Undantagsmeddelanden|
|------------------------|
|Antalet rader i indatauppsättningen är mindre än tillåtet minimum.|
|Antalet rader i indatauppsättningen är mindre än tillåtet minimum av {required_rows_count} rader.|
|Antalet rader i indatauppsättningen är mindre än tillåtet minimum av {required_rows_count} rader. {orsak}|
|Antalet rader i indatauppsättningen {arg_name}är mindre än tillåtet minimum av {required_rows_count} rader.|
|Antalet rader i indatauppsättningen {arg_name}, är {actual_rows_count}, mindre än det tillåtna minimum av {required_rows_count} rader.|
|Antalet rader med {row_type}i indatauppsättningen {arg_name}, är {actual_rows_count}, mindre än tillåtna minimum av {required_rows_count} rader.|


## <a name="error-0022"></a>Fel 0022  
 Undantag inträffar om antalet markerade kolumner i indatauppsättningen inte är lika med det förväntade talet.  

 Det här felet i Azure Machine Learning kan uppstå när den underordnade modulen eller åtgärden kräver ett visst antal kolumner eller indata och du har angett för få eller för många kolumner eller indata. Ett exempel:  

-   Du anger en kolumn eller nyckelkolumn med en etikett och har markerat flera kolumner av misstag.  
  
-   Du byter namn på kolumner, men angav fler eller färre namn än det finns kolumner.  
  
-   Antalet kolumner i källan eller målet har ändrats eller matchar inte antalet kolumner som används av modulen.  
  
-   Du har angett en kommaavgränsad lista med värden för indata, men antalet värden matchar inte eller flera indata stöds inte.  

**Upplösning:** Gå tillbaka till modulen och kontrollera kolumnvalet för att säkerställa att rätt antal kolumner är markerat. Verifiera utdata från uppströmsmoduler och kraven för nedströmsoperationer.  

 Om du har använt ett av kolumnmarkeringsalternativen som kan markera flera kolumner (kolumnindex, alla funktioner, alla numeriska osv.) validerar du det exakta antalet kolumner som returneras av markeringen.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Kontrollera att antalet eller typen av uppströmskolumner inte har ändrats.  

 Om du använder en rekommendationsdatauppsättning för att träna en modell, kom ihåg att användaren förväntar sig ett begränsat antal kolumner, vilket motsvarar användarartikelpar eller användarobjektrankningar. Ta bort ytterligare kolumner innan du tränar modellen eller delar upp rekommendationsdatauppsättningar. Mer information finns i [Dela data](split-data.md).  

|Undantagsmeddelanden|
|------------------------|
|Antalet markerade kolumner i indatauppsättningen är inte lika med det förväntade talet.|
|Antalet markerade kolumner i indatauppsättningen är inte lika med {expected_col_count}.|
|Kolumnmarkeringsmönstret {selection_pattern_friendly_name}, anger antalet markerade kolumner i indatauppsättningen som inte är lika med {expected_col_count}.|
|Kolumnmarkeringsmönstret {selection_pattern_friendly_name}, förväntas tillhandahålla {expected_col_count} kolumner som valts i indatauppsättningen, men {selected_col_count} kolumner tillhandahålls faktiskt.|


## <a name="error-0023"></a>Fel 0023  

Undantag inträffar om målkolumnen för indatauppsättning inte är giltig för den aktuella trainermodulen.  

Det här felet i Azure Machine Learning inträffar om målkolumnen (som valts i modulparametrarna) inte är av giltig datatyp, innehöll alla värden som saknas eller inte var kategorisk som förväntat.  

**Upplösning:** Gå tillbaka till modulindata för att granska innehållet i etikett-/målkolumnen. Kontrollera att den inte har alla värden som saknas. Om modulen förväntar sig att målkolumnen ska vara kategorisk kontrollerar du att det finns fler än ett distinkt värde i målkolumnen.  

|Undantagsmeddelanden|
|------------------------|
|Indatauppsättningen har inte stödmålskolumnen.|
|Indatauppsättningen har målkolumnen som inte stöds {column_index}.|
|Indatauppsättningen har målkolumnen "{column_index} som stöds för eleven av typen {learner_type}.|


## <a name="error-0024"></a>Fel 0024  
Undantag inträffar om datauppsättningen inte innehåller någon etikettkolumn.  

 Det här felet i Azure Machine Learning uppstår när modulen kräver en etikettkolumn och datauppsättningen inte har en etikettkolumn. Utvärdering av en poängsatt datauppsättning kräver till exempel vanligtvis att en etikettkolumn finns för att beräkna noggrannhetsmått.  

Det kan också hända att en etikettkolumn finns i datauppsättningen, men inte identifieras korrekt av Azure Machine Learning.

**Upplösning:**

+ Öppna modulen som genererade felet och ta reda på om det finns en etikettkolumn. Namnet eller datatypen för kolumnen spelar ingen roll, så länge kolumnen innehåller ett enda resultat (eller beroende variabel) som du försöker förutsäga. Om du är osäker på vilken kolumn som har etiketten letar du efter ett generiskt namn som *Klass* eller *Mål*. 
+  Om datauppsättningen inte innehåller en etikettkolumn är det möjligt att etikettkolumnen uttryckligen eller av misstag har tagits bort uppströms. Det kan också vara så att datauppsättningen inte är utdata från en poängsättningsmodul uppströms.
+ Om du uttryckligen vill markera kolumnen som etikettkolumn lägger du till modulen [Redigera metadata](edit-metadata.md) och ansluter datauppsättningen. Markera bara etikettkolumnen och välj **Etikett** i listrutan **Fält.** 
+ Om fel kolumn väljs som etikett kan du välja **Rensa etikett** från **fälten** för att åtgärda metadata i kolumnen. 
  
|Undantagsmeddelanden|
|------------------------|
|Det finns ingen etikettkolumn i datauppsättningen.|
|Det finns ingen etikettkolumn i {dataset_name}.|


## <a name="error-0025"></a>Fel 0025  
 Undantag inträffar om datauppsättningen inte innehåller en poängkolumn.  

 Det här felet i Azure Machine Learning inträffar om indata till utvärdera-modellen inte innehåller giltiga poängkolumner. Användaren försöker till exempel utvärdera en datauppsättning innan den gjordes med en korrekt tränad modell, eller så har poängkolumnen uttryckligen tagits bort uppströms. Det här undantaget inträffar också om poängkolumnerna på de två datauppsättningarna är inkompatibla. Du kanske till exempel försöker jämföra noggrannheten hos en linjär regressor med en binär klassificerare.  

**Upplösning:** Gå tillbaka till indata till utvärdera-modellen och undersök om den innehåller en eller flera poängkolumner. Om inte, gjordes inte datauppsättningen eller poängkolumnerna tappades i en uppströmsmodul.  

|Undantagsmeddelanden|
|------------------------|
|Det finns ingen poängkolumn i datauppsättningen.|
|Det finns ingen poängkolumn i {dataset_name}.|
|Det finns ingen poängkolumn i {dataset_name}" som produceras av en {learner_type}. Betygsdatauppsättningen med rätt typ av elev.|


## <a name="error-0026"></a>Fel 0026  
 Undantag inträffar om kolumner med samma namn inte är tillåtna.  

 Det här felet i Azure Machine Learning inträffar om flera kolumner har samma namn. Ett sätt du kan få det här felet är om datauppsättningen inte har en rubrikrad och kolumnnamn tilldelas automatiskt: Col0, Col1, etc.  

**Upplösning:** Om kolumnerna har samma namn infogar du en [redigera metadatamodul](edit-metadata.md) mellan indatauppsättningen och modulen. Använd kolumnväljaren i [Redigera metadata](edit-metadata.md) för att markera kolumner att byta namn på och skriv de nya namnen i textrutan **Nya kolumnnamn.**  

|Undantagsmeddelanden|
|------------------------|
|Lika kolumnnamn anges i argument. Likhetskolumnnamn tillåts inte per modul.|
|Lika kolumnnamn i argumenten {arg_name_1}" och {arg_name_2}. Ange olika namn.|


## <a name="error-0027"></a>Fel 0027  
 Undantag inträffar om två objekt måste vara av samma storlek men inte.  

 Detta är ett vanligt fel i Azure Machine Learning och kan orsakas av många villkor.  

**Upplösning:** Det finns ingen specifik resolution. Du kan dock kontrollera om det finns följande villkor:  

-   Om du byter namn på kolumner kontrollerar du att varje lista (indatakolumnerna och listan med nya namn) har samma antal objekt.  
  
-   Om du ansluter eller sammanfogar två datauppsättningar kontrollerar du att de har samma schema.  
  
-   Om du sammanfogar två datauppsättningar som har flera kolumner kontrollerar du att nyckelkolumnerna har samma datatyp och väljer alternativet **Tillåt dubbletter och bevarar kolumnordning i markeringen**.  

|Undantagsmeddelanden|
|------------------------|
|Storleken på godkända objekt är inkonsekvent.|
|Storleken på {friendly_name1}" är inkonsekvent med storleken på {friendly_name2}.|


## <a name="error-0028"></a>Fel 0028  
 Undantag inträffar i det fall då kolumnuppsättningen innehåller dubblerade kolumnnamn och det är inte tillåtet.  

 Det här felet i Azure Machine Learning uppstår när kolumnnamn dupliceras. det vill än, inte unikt.  

**Upplösning:** Om några kolumner har samma namn lägger du till en förekomst av [Redigera metadata](edit-metadata.md) mellan indatauppsättningen och modulen som höjer felet. Använd kolumnväljaren i [Redigera metadata](edit-metadata.md) för att markera kolumner som ska byta namn och skriv de nya kolumnnamnen i textrutan **Nya kolumnnamn.** Om du byter namn på flera kolumner kontrollerar du att värdena du skriver i de **nya kolumnnamnen** är unika.  

|Undantagsmeddelanden|
|------------------------|
|Kolumnuppsättningen innehåller dubblerade kolumnnamn.|
|Namnet {duplicated_name}" dupliceras.|
|Namnet {duplicated_name}" dupliceras i {arg_name}.|
|Namnet {duplicated_name}" dupliceras. Detaljer: {details}|


## <a name="error-0029"></a>Fel 0029  
 Undantag inträffar om ogiltig URI skickas.  

 Det här felet i Azure Machine Learning uppstår i fall när ogiltig URI skickas.  Du kommer att få det här felet om något av följande villkor är sant:  

-   Den offentliga eller SAS URI som tillhandahålls för Azure Blob Storage för läsning eller skrivning innehåller ett fel.  
  
-   Tidsfönstret för SAS har upphört att gälla.  
  
-   Webb-URL:en via HTTP-källa representerar en fil eller en loopback-URI.  
  
-   Webb-URL:en via HTTP innehåller en felaktigt formaterad URL.  
  
-   URL:en kan inte matchas av fjärrkällan.  

**Upplösning:** Gå tillbaka till modulen och verifiera formatet på URI:n. Om datakällan är en webb-URL via HTTP kontrollerar du att den avsedda källan inte är en fil eller en loopback-URI (localhost).  

|Undantagsmeddelanden|
|------------------------|
|Ogiltig Uri skickas.|
|Uri "{invalid_url}" är ogiltig.|


## <a name="error-0030"></a>Fel 0030  
 Undantag inträffar i det fall då det inte är möjligt att hämta en fil.  

 Det här undantaget i Azure Machine Learning inträffar när det inte är möjligt att hämta en fil. Du får det här undantaget när ett försök att läsa från en HTTP-källa har misslyckats efter tre (3) försök att försöka igen.  

**Upplösning:** Kontrollera att URI:n till HTTP-källan är korrekt och att webbplatsen för närvarande är tillgänglig via Internet.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att hämta en fil.|
|Det gick inte att hämta filen: {file_url}.|


## <a name="error-0031"></a>Fel 0031  
 Undantag inträffar om antalet kolumner i kolumnuppsättningen är mindre än nödvändigt.  

 Det här felet i Azure Machine Learning inträffar om antalet valda kolumner är mindre än nödvändigt.  Det här felet visas om det minsta antalet kolumner inte är markerat.  

**Upplösning:** Lägg till ytterligare kolumner i kolumnmarkeringen med hjälp av **kolumnväljaren**.  

|Undantagsmeddelanden|
|------------------------|
|Antalet kolumner i kolumnuppsättningen är mindre än vad som krävs.|
|Minst {required_columns_count} kolumner ska anges för indataargumentet {arg_name}.|
|Minst {required_columns_count} kolumner ska anges för indataargumentet {arg_name}. Det faktiska antalet angivna kolumner är {input_columns_count}.|


## <a name="error-0032"></a>Fel 0032  
 Undantag inträffar om argumentet inte är ett tal.  

 Det här felet visas i Azure Machine Learning om argumentet är en dubbel eller NaN.  

**Upplösning:** Ändra det angivna argumentet om du vill använda ett giltigt värde.  

|Undantagsmeddelanden|
|------------------------|
|Argument är inte ett tal.|
|{{arg_name}" är inte ett tal.|


## <a name="error-0033"></a>Fel 0033  
 Undantag inträffar om argumentet är Oändlighet.  

 Det här felet i Azure Machine Learning uppstår om argumentet är oändligt. Det här felet visas om `double.NegativeInfinity` argumentet `double.PositiveInfinity`är antingen eller .  

**Upplösning:** Ändra det angivna argumentet så att det är ett giltigt värde.  

|Undantagsmeddelanden|
|------------------------|
|Argumentet måste vara begränsat.|
|"{arg_name}" är inte ändligt.|


## <a name="error-0034"></a>Fel 0034  
 Undantag inträffar om det finns mer än en klassificering för ett visst användarobjektpar.  

 Det här felet i Azure Machine Learning uppstår i rekommendation om ett användarobjektpar har mer än ett klassificeringsvärde.  

**Upplösning:** Kontrollera att användarartikelparet endast har ett klassificeringsvärde.  

|Undantagsmeddelanden|
|------------------------|
|Det finns mer än en klassificering för värdet/värdena i datauppsättningen.|
|Mer än en klassificering för användaren {user} och objektet {item} i datatabellen för förutsägelse av klassificering.|
|Mer än en klassificering för användaren {user} och objektet {item} i {dataset}.|


## <a name="error-0035"></a>Fel 0035  
 Undantag inträffar om inga funktioner har angetts för en viss användare eller ett visst objekt.  

 Det här felet i Azure Machine Learning uppstår som du försöker använda en rekommendationsmodell för bedömning men det går inte att hitta en funktionsvektor.  

**Upplösning:**

Den Matchbox recommender har vissa krav som måste uppfyllas när du använder antingen objektfunktioner eller användarfunktioner.  Det här felet indikerar att en funktionsvektor saknas för en användare eller ett objekt som du angav som indata. Kontrollera att en vektor med funktioner är tillgänglig i data för varje användare eller objekt.  

 Om du till exempel har tränat en rekommendationsmodell med funktioner som användarens ålder, plats eller inkomst, men nu vill skapa poäng för nya användare som inte sågs under utbildningen, måste du ange motsvarande uppsättning funktioner (nämligen ålder, plats och inkomstvärden) för de nya användarna för att kunna göra lämpliga förutsägelser för dem. 

 Om du inte har några funktioner för dessa användare bör du överväga funktionsteknik för att generera lämpliga funktioner.  Om du till exempel inte har enskilda användarålders- eller inkomstvärden kan du generera ungefärliga värden som ska användas för en grupp användare. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Resolution som inte är tillämplig på ditt ärende? Du är välkommen att skicka feedback på den här artikeln och ge information om scenariot, inklusive modulen och antalet rader i kolumnen. Vi kommer att använda den här informationen för att tillhandahålla mer detaljerade felsökningssteg i framtiden.

|Undantagsmeddelanden|
|------------------------|
|Inga funktioner har angetts för en nödvändig användare eller ett objekt som krävs.|
|Funktioner för {required_feature_name} krävs men inte.|


## <a name="error-0036"></a>Fel 0036  
 Undantag inträffar om flera funktionsvektorer har tillhandahållits för en viss användare eller ett visst objekt.  

 Det här felet i Azure Machine Learning uppstår om en funktionsvektor har definierats mer än en gång.  

**Upplösning:** Kontrollera att funktionsvektorn inte har definierats mer än en gång.  

|Undantagsmeddelanden|
|------------------------|
|Duplicera funktionsdefinition för en användare eller ett objekt.|


## <a name="error-0037"></a>Fel 0037  
 Undantag inträffar om flera etikettkolumner anges och bara en tillåts.  

 Det här felet i Azure Machine Learning inträffar om mer än en kolumn har valts för att vara den nya etikettkolumnen. De flesta övervakade inlärningsalgoritmer kräver att en enda kolumn markeras som mål eller etikett.  

**Upplösning:** Se till att markera en enskild kolumn som den nya etikettkolumnen.  

|Undantagsmeddelanden|
|------------------------|
|Flera etikettkolumner har angetts.|
|Flera etikettkolumner anges i {dataset_name}.|


## <a name="error-0039"></a>Fel 0039  
 Undantag inträffar om en åtgärd har misslyckats.  

 Det här felet i Azure Machine Learning uppstår när en intern åtgärd inte kan slutföras.  

**Upplösning:** Detta fel orsakas av många villkor och det finns ingen specifik åtgärd.  
 Följande tabell innehåller allmänna meddelanden för det här felet, som följs av en specifik beskrivning av villkoret. 

 Om det inte finns några detaljer [tillgängliga, skicka feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) och ge information om de moduler som genererade felet och relaterade villkor.

|Undantagsmeddelanden|
|------------------------|
|Åtgärden misslyckades.|
|Fel vid slutförande av åtgärden: "{failed_operation}".|
|Fel vid slutförande av åtgärden: "{failed_operation}". Orsak: "{reason}".|


## <a name="error-0042"></a>Fel 0042  
 Undantag inträffar när det inte är möjligt att konvertera kolumn till en annan typ.  

 Det här felet i Azure Machine Learning uppstår när det inte är möjligt att konvertera kolumn till den angivna typen.  Det här felet visas om en modul kräver en viss datatyp, till exempel datumtid, text, ett flyttalsnummer eller heltal, men det går inte att konvertera en befintlig kolumn till den önskade typen.  

Du kan till exempel välja en kolumn och försöka konvertera den till en numerisk datatyp för användning i en matematisk åtgärd och få det här felet om kolumnen innehåller ogiltiga data. 

En annan orsak till att du kan få det här felet om du försöker använda en kolumn som innehåller flyttalsnummer eller många unika värden som en kategorisk kolumn. 

**Upplösning:**

+ Öppna hjälpsidan för modulen som genererade felet och kontrollera kraven på datatyp.
+ Granska datatyperna för kolumnerna i indatauppsättningen.
+ Kontrollera data som kommer från så kallade schemalösa datakällor.
+ Kontrollera datauppsättningen för saknade värden eller specialtecken som kan blockera konvertering till önskad datatyp. 
    + Numeriska datatyper bör vara konsekventa: kontrollera till exempel flyttalsnummer i en kolumn med heltal.
    + Leta efter textsträngar eller NA-värden i en talkolumn. 
    + Booleska värden kan konverteras till en lämplig representation beroende på vilken datatyp som krävs.
    + Granska textkolumner för tecken som inte är unicode-tecken, tabbtecken eller kontrolltecken
    + Datetime-data bör vara konsekventa för att undvika modelleringsfel, men rensningen kan vara komplex på grund av de många formaten. Överväg att använda <!--the [Execute R Script](execute-r-script.md) or -->[Kör Python Script-moduler](execute-python-script.md) för rensning.  
+ Om det behövs ändrar du värdena i indatauppsättningen så att kolumnen kan konverteras. Ändringen kan omfatta binning, trunkering eller avrundning, eliminering av extremvärden eller imputering av saknade värden. Se följande artiklar för några vanliga scenarier för dataomvandling i maskininlärning:
    + [Rensa data som saknas](clean-missing-data.md)
    + [Normalisera Data](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Resolution oklar, eller inte tillämplig på ditt fall? Du är välkommen att skicka feedback på den här artikeln och ge information om scenariot, inklusive modulen och datatypen för kolumnen. Vi kommer att använda den här informationen för att tillhandahålla mer detaljerade felsökningssteg i framtiden.  

|Undantagsmeddelanden|
|------------------------|
|Tillåts inte konvertering.|
|Det gick inte att konvertera kolumn av typen {type1} till kolumn av typen {type2}.|
|Det gick inte att konvertera kolumnen {col_name1}" av typen {type1} till kolumnen av typen {type2}.|
|Det gick inte att konvertera kolumnen {col_name1}" av typen {type1} till kolumnen {col_name2}" av typen {type2}.|


## <a name="error-0044"></a>Fel 0044  
 Undantag inträffar när det inte är möjligt att härleda elementtyp av kolumn från de befintliga värdena.  

 Det här felet i Azure Machine Learning uppstår när det inte är möjligt att dra slutsatsen vilken typ av kolumn eller kolumner i en datauppsättning. Detta händer vanligtvis när du sammanfogar två eller flera datauppsättningar med olika elementtyper. Om Azure Machine Learning inte kan avgöra en gemensam typ som kan representera alla värden i en kolumn eller kolumner utan förlust av information, genererar den det här felet.  

**Upplösning:** Kontrollera att alla värden i en viss kolumn i båda datauppsättningarna som kombineras är antingen av samma typ (numeriskt, booleskt, kategoriskt, sträng, datum osv.) eller kan tvingas till samma typ.  

|Undantagsmeddelanden|
|------------------------|
|Det går inte att härleda elementtypen för kolumnen.|
|Det går inte att härleda elementtypen för kolumnen {column_name}- alla element är null-referenser.|
|Det går inte att härleda elementtypen för kolumnen {column_name}" i datauppsättningen {dataset_name}- alla element är null-referenser.|


## <a name="error-0045"></a>Fel 0045  
 Undantag inträffar när det inte går att skapa en kolumn på grund av blandade elementtyper i källan.  

 Det här felet i Azure Machine Learning uppstår när elementtyperna av två datauppsättningar som kombineras är olika.  

**Upplösning:** Kontrollera att alla värden i en viss kolumn i båda datauppsättningarna som kombineras är av samma typ (numeriskt, booleskt, kategoriskt, sträng, datum osv.).  

|Undantagsmeddelanden|
|------------------------|
|Det går inte att skapa kolumn med blandade elementtyper.|
|Det går inte att skapa kolumnen med id "{column_id}" av blandade elementtyper:<br />Typ av data[{row_1}, {column_id}] är {type_1}.Type of data[{row_1}, {column_id}] is{type_1}". <br />Typ av data[{row_2}, {column_id}] är {type_2}".|
|Det går inte att skapa kolumnen med id "{column_id}" av blandade elementtyper:<br />Skriv i segment {chunk_id_1} är {type_1}. <br />Typ i segment {chunk_id_2} är {type_2}" med segmentstorlek: {chunk_size}.|


## <a name="error-0046"></a>Fel 0046  
 Undantag inträffar när det inte är möjligt att skapa katalog på angiven sökväg.  

 Det här felet i Azure Machine Learning uppstår när det inte är möjligt att skapa en katalog på den angivna sökvägen. Det här felet visas om någon del av sökvägen till utdatakatalogen för en Hive-fråga är felaktig eller otillgänglig.  

**Upplösning:** Gå tillbaka till modulen och kontrollera att katalogsökvägen är korrekt formaterad och att den är tillgänglig med de aktuella autentiseringsuppgifterna.  

|Undantagsmeddelanden|
|------------------------|
|Ange en giltig utdatakatalog.|
|Katalog: {sökväg} kan inte skapas. Ange giltig sökväg.|


## <a name="error-0047"></a>Fel 0047  
 Undantag inträffar om antalet funktionskolumner i vissa av de datauppsättningar som skickas till modulen är för litet.  

 Det här felet i Azure Machine Learning inträffar om indatauppsättningen till utbildning inte innehåller det minsta antalet kolumner som krävs av algoritmen. Vanligtvis är datauppsättningen tom eller bara träningskolumner.  

**Upplösning:** Gå tillbaka till indatauppsättningen för att se till att det finns ytterligare en eller flera kolumner förutom etikettkolumnen.  

|Undantagsmeddelanden|
|------------------------|
|Antalet funktionskolumner i indatauppsättningen är mindre än tillåtet minimum.|
|Antalet funktionskolumner i indatauppsättningen är mindre än tillåtet minimum av {required_columns_count} kolumner.|
|Antalet funktionskolumner i indatauppsättningen {arg_name}" är mindre än tillåtet minimum av {required_columns_count} kolumner.|


## <a name="error-0048"></a>Fel 0048  
 Undantag inträffar i det fall då det inte är möjligt att öppna en fil.  

 Det här felet i Azure Machine Learning uppstår när det inte är möjligt att öppna en fil för läsning eller skrivning. Det här felet kan visas av följande skäl:  

-   Behållaren eller filen (blob) finns inte  
  
-   Åtkomstnivån för filen eller behållaren gör det inte möjligt för dig att komma åt filen  
  
-   Filen är för stor för att läsas eller fel format  

**Upplösning:** Gå tillbaka till modulen och filen som du försöker läsa.  

 Kontrollera att namnen på behållaren och filen är korrekta.  

 Använd den klassiska Azure-portalen eller ett Azure-lagringsverktyg för att verifiera att du har behörighet att komma åt filen.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att öppna en fil.|
|Det gick inte att öppna filen: {file_name}.|
|Det gick inte att öppna filen: {file_name}. Undantagsmeddelande för lagring: {exception}.|


## <a name="error-0049"></a>Fel 0049  
 Undantag inträffar i det fall då det inte är möjligt att tolka en fil.  

 Det här felet i Azure Machine Learning uppstår när det inte är möjligt att tolka en fil. Det här felet visas om det filformat som valts i modulen [Importera data](import-data.md) inte matchar filens faktiska format eller om filen innehåller ett oigenkännligt tecken.  

**Upplösning:** Gå tillbaka till modulen och korrigera valet av filformat om den inte matchar filens format. Om möjligt bör du kontrollera filen för att bekräfta att den inte innehåller några ogiltiga tecken.  

|Undantagsmeddelanden|
|------------------------|
|Det går inte att tolka en fil.|
|Fel vid tolkning av filen {file_format}.|
|Fel vid tolkning av filen {file_format} : {file_name}.|
|Fel vid tolkning av filen {file_format}. Orsak: {failure_reason}.|
|Fel vid tolkning av filen {file_format} : {file_name}. Orsak: {failure_reason}.|


## <a name="error-0052"></a>Fel 0052  
 Undantag inträffar om Azure-lagringskontonyckeln har angetts felaktigt.  

 Det här felet i Azure Machine Learning inträffar om nyckeln som används för att komma åt Azure-lagringskontot är felaktigt. Det här felet kan till exempel visas om Azure-lagringsnyckeln trunkerades när den kopierades och klistrades in, eller om fel nyckel användes.  

 Mer information om hur du hämtar nyckeln för ett Azure-lagringskonto finns i [Visa, kopiera och återskapa lagringsåtkomstnycklar](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  

**Upplösning:** Gå tillbaka till modulen och kontrollera att Azure-lagringsnyckeln är korrekt för kontot. kopiera nyckeln igen från den klassiska Azure-portalen om det behövs.  

|Undantagsmeddelanden|
|------------------------|
|Azure-lagringskontonyckeln är felaktig.|


## <a name="error-0053"></a>Fel 0053  
 Undantag inträffar i fallet när det inte finns några användarfunktioner eller objekt för matchboxrekommendationer.  

 Det här felet i Azure Machine Learning skapas när det inte går att hitta en funktionsvektor.  

**Upplösning:** Kontrollera att det finns en funktionsvektor i indatauppsättningen.  

|Undantagsmeddelanden|
|------------------------|
|Användarfunktioner eller/och objekt krävs men tillhandahålls inte.|


## <a name="error-0056"></a>Fel 0056  
 Undantag inträffar om de kolumner som du har valt för en åtgärd bryter mot kraven.  

 Det här felet i Azure Machine Learning uppstår när du väljer kolumner för en åtgärd som kräver att kolumnen är av en viss datatyp. 

 Det här felet kan också inträffa om kolumnen är rätt datatyp, men modulen du använder kräver att kolumnen också markeras som en funktion, etikett eller kategorisk kolumn.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Upplösning:**

1.  Granska datatypen för de kolumner som för närvarande är markerade. 

2. Ta reda på om de markerade kolumnerna är kategoriska, etikett- eller funktionskolumner.  
  
3.  Granska hjälpavsnittet för modulen där du gjorde kolumnvalet för att avgöra om det finns särskilda krav för datatyp eller kolumnanvändning.  
  
3.  Använd [Redigera metadata](edit-metadata.md) för att ändra kolumntypen under åtgärdens varaktighet. Var noga med att ändra kolumntypen tillbaka till sitt ursprungliga värde, med hjälp av en annan instans av [Redigera metadata](edit-metadata.md), om du behöver den för nedströmsåtgärder.  

|Undantagsmeddelanden|
|------------------------|
|En eller flera markerade kolumner fanns inte i en tillåten kategori.|
|Kolumnen med namnet {col_name}.|


## <a name="error-0057"></a>Fel 0057  
 Undantag inträffar när du försöker skapa en fil eller blob som redan finns.  

 Det här undantaget inträffar när du använder modulen [Exportera data](export-data.md) eller annan modul för att spara resultaten av en pipeline i Azure Machine Learning till Azure blob storage, men du försöker skapa en fil eller blob som redan finns.   

**Upplösning:**

 Det här felet visas bara om du tidigare har angett att egenskapen **Azure blob storage write mode** till **Error**. Den här modulen uppstår avsiktligt ett fel om du försöker skriva en datauppsättning till en blob som redan finns.

 - Öppna modulegenskaperna och ändra egenskapen **azure blob storage write mode** till Skriv **över**.
 - Du kan också skriva namnet på en annan målblob eller -fil och se till att ange en blob som inte redan finns.  

|Undantagsmeddelanden|
|------------------------|
|Filen eller Blob finns redan.|
|Filen eller Bloben {file_path}finns redan.|


## <a name="error-0058"></a>Fel 0058  
 Det här felet i Azure Machine Learning inträffar om datauppsättningen inte innehåller den förväntade etikettkolumnen.  

 Det här undantaget kan också uppstå när etikettkolumnen som anges inte matchar de data eller den datatyp som eleven förväntas eller har fel värden. Det här undantaget skapas till exempel när du använder en etikettkolumn med riktiga värden när du tränar en binär klassificerare.  

**Upplösning:** Upplösningen beror på vilken elev eller utbildare du använder och datatyperna för kolumnerna i datauppsättningen. Kontrollera först kraven för maskininlärningsalgoritmen eller utbildningsmodulen.  

 Gå tillbaka till indatauppsättningen. Kontrollera att kolumnen som du förväntar dig ska behandlas som etiketten har rätt datatyp för den modell du skapar.  

 Kontrollera indata efter saknade värden och eliminera eller byt ut dem om det behövs.  

 Om det behövs lägger du till modulen [Redigera metadata](edit-metadata.md) och kontrollerar att etikettkolumnen är markerad som en etikett.  

|Undantagsmeddelanden|
|------------------------|
|Etikettkolumnvärdena och poängsatta etikettkolumnvärdena är inte jämförbara.|
|Etikettkolumnen är inte som förväntat i {dataset_name}.|
|Etikettkolumnen är inte som förväntat i {dataset_name}, {reason}.|
|Etikettkolumnen {column_name} förväntas inte i {dataset_name}.|
|Etikettkolumnen {column_name} förväntas inte i {dataset_name}, {reason}.|


## <a name="error-0059"></a>Fel 0059  
 Undantag inträffar om ett kolumnindex som anges i en kolumnväljare inte kan tolkas.  

 Det här felet i Azure Machine Learning uppstår om ett kolumnindex som anges när kolumnväljaren inte kan tolkas.  Det här felet visas när kolumnindexet är i ett ogiltigt format som inte kan tolkas.  

**Upplösning:** Ändra kolumnindexet om du vill använda ett giltigt indexvärde.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att tolka ett eller flera angivna kolumnindex eller indexintervall.|
|Kolumnindex eller -område {column_index_or_range}kunde inte tolkas.|


## <a name="error-0060"></a>Fel 0060  
 Undantag inträffar när ett kolumnintervall utanför intervallet anges i en kolumnväljare.  

 Det här felet i Azure Machine Learning uppstår när ett kolumnintervall utanför intervallet anges i kolumnväljaren. Det här felet visas om kolumnintervallet i kolumnväljaren inte motsvarar kolumnerna i datauppsättningen.  

**Upplösning:** Ändra kolumnområdet i kolumnväljaren så att det motsvarar kolumnerna i datauppsättningen.  

|Undantagsmeddelanden|
|------------------------|
|Ogiltigt eller utanför det angivna indexintervallet för intervallet.|
|Kolumnområdet {column_range}" är ogiltigt eller utanför intervallet.|


## <a name="error-0061"></a>Fel 0061  
 Undantag inträffar när du försöker lägga till en rad i en DataTable som har ett annat antal kolumner än tabellen.  

 Det här felet i Azure Machine Learning uppstår när du försöker lägga till en rad i en datauppsättning som har ett annat antal kolumner än datauppsättningen.  Det här felet visas om raden som läggs till i datauppsättningen har ett annat antal kolumner än indatauppsättningen.  Det går inte att lägga till raden i datauppsättningen om antalet kolumner är olika.  

**Upplösning:** Ändra indatauppsättningen om du vill ha samma antal kolumner som raden har lagts till, eller ändra raden som läggs till för att ha samma antal kolumner som datauppsättningen.  

|Undantagsmeddelanden|
|------------------------|
|Alla tabeller måste ha samma antal kolumner.|
|Kolumner i segmentet {chunk_id_1}" skiljer sig åt med segmentet {chunk_id_2}" med segmentstorlek: {chunk_size}.|
|Kolumnantalet i filen {filename_1}" (count={column_count_1}) skiljer sig åt med filen {filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Fel 0062  
 Undantag inträffar när du försöker jämföra två modeller med olika elevtyper.  

 Det här felet i Azure Machine Learning uppstår när utvärderingsmått för två olika poängsatta datauppsättningar inte kan jämföras. I detta fall är det inte möjligt att jämföra effektiviteten hos de modeller som används för att producera de två poängsatta datauppsättningarna.  

**Upplösning:** Kontrollera att de poängsatta resultaten produceras av samma typ av maskininlärningsmodell (binär klassificering, regression, klassificering av flera klasser, rekommendation, klustring, avvikelseidentifiering osv.) Alla modeller som du jämför måste ha samma elevtyp.  

|Undantagsmeddelanden|
|------------------------|
|Alla modeller måste ha samma elevtyp.|
|Fick inkompatibel elevtyp: "{actual_learner_type}". Förväntade elevtyper är: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Fel 0064  
 Undantag inträffar om Azure-lagringskontonamn eller lagringsnyckel har angetts felaktigt.  

 Det här felet i Azure Machine Learning inträffar om Azure-lagringskontots namn eller lagringsnyckel anges felaktigt. Det här felet visas om du anger ett felaktigt kontonamn eller lösenord för lagringskontot. Detta kan inträffa om du anger kontonamnet eller lösenordet manuellt. Det kan också inträffa om kontot har tagits bort.  

**Upplösning:** Kontrollera att kontonamnet och lösenordet har angetts korrekt och att kontot finns.  

|Undantagsmeddelanden|
|------------------------|
|Azure-lagringskontonamnet eller lagringsnyckeln är felaktig.|
|Azure-lagringskontonamnet {account_name}" eller lagringsnyckeln för kontonamnet är felaktigt.|


## <a name="error-0065"></a>Fel 0065  
 Undantag inträffar om Azure blob-namnet anges felaktigt.  

 Det här felet i Azure Machine Learning inträffar om Azure-blobnamnet anges felaktigt.  Felet visas om:  

-   Bloben kan inte hittas i den angivna behållaren.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Endast behållaren angavs som källa i en begäran om [importdata](import-data.md) när formatet var Excel eller CSV med kodning. sammanfogning av innehållet i alla blobbar i en behållare är inte tillåtet med dessa format.  
  
-   En SAS URI innehåller inte namnet på en giltig blob.  

**Upplösning:** Gå tillbaka till modulen som kastar undantaget. Kontrollera att den angivna blobben finns i behållaren i lagringskontot och att behörigheterna gör att du kan se blobben. Kontrollera att indata är av formuläret **containernamn/filnamn** om du har Excel eller CSV med kodningsformat. Kontrollera att en SAS URI innehåller namnet på en giltig blob.  

|Undantagsmeddelanden|
|------------------------|
|Azure-lagringsblobnamnet är felaktigt.|
|Azure-lagringsblobbnamnet {blob_name}är felaktigt.|
|Azure storage blob name with prefix "{blob_name_prefix}" finns inte.|
|Det gick inte att hitta några Azure-lagringsblobar under behållaren {container_name}.Failed to find any Azure storage blobs under container {{container_name}".|
|Det gick inte att hitta några Azure-lagringsblobar med jokerteckensökvägen {blob_wildcard_path}.Failed to find any Azure storage blobs with wildcard path "{blob_wildcard_path}".|


## <a name="error-0066"></a>Fel 0066  
 Undantag inträffar om en resurs inte kunde överföras till en Azure Blob.  

 Det här felet i Azure Machine Learning inträffar om en resurs inte kunde överföras till en Azure Blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Båda sparas i samma Azure-lagringskonto som kontot som innehåller indatafilen.  

**Upplösning:** Gå tillbaka till modulen. Kontrollera att Azure-kontonamn, lagringsnyckel och behållare är korrekta och att kontot har behörighet att skriva till behållaren.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att överföra resursen till Azure-lagring.|
|Det gick inte att överföra filen {source_path}till Azure-lagring som {dest_path}.The file {source_path}" could not be uploaded to Azure storage as {dest_path}".|


## <a name="error-0067"></a>Fel 0067  
 Undantag inträffar om en datauppsättning har ett annat antal kolumner än förväntat.  

 Det här felet i Azure Machine Learning uppstår om en datauppsättning har ett annat antal kolumner än förväntat.  Det här felet visas när antalet kolumner i datauppsättningen skiljer sig från antalet kolumner som modulen förväntar sig under körningen.  

**Upplösning:** Ändra indatauppsättningen eller parametrarna.  

|Undantagsmeddelanden|
|------------------------|
|Oväntat antal kolumner i datatabellen.|
|Oväntat antal kolumner i datauppsättningen {dataset_name}.|
|Förväntade kolumnerna {expected_column_count}, men hittade kolumnerna {actual_column_count}i stället.|
|I indatauppsättningen {dataset_name}, förväntade kolumnerna {expected_column_count}" men {actual_column_count}-kolumnerna hittades i stället.|


## <a name="error-0068"></a>Fel 0068  
 Undantag inträffar om det angivna Hive-skriptet inte är korrekt.  

 Det här felet i Azure Machine Learning uppstår om det finns syntaxfel i ett Hive QL-skript, eller om Hive-tolken stöter på ett fel när frågan eller skriptet körs.  

**Upplösning:**

Felmeddelandet från Hive rapporteras normalt tillbaka i felloggen så att du kan vidta åtgärder baserat på det specifika felet. 

+ Öppna modulen och kontrollera frågan om det finns misstag.  
+ Kontrollera att frågan fungerar korrekt utanför Azure Machine Learning genom att logga in på Hive-konsolen i Ditt Hadoop-kluster och köra frågan.  
+ Prova att placera kommentarer i ditt Hive-skript i en separat rad i stället för att blanda körbara uttalanden och kommentarer på en enda rad.  

### <a name="resources"></a>Resurser

Mer information finns i följande artiklar om hjälp med Hive-frågor för maskininlärning:

+ [Skapa Hive-tabeller och läsa in data från Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Utforska data i tabeller med Hive-frågor](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Skapa funktioner för data i ett Hadoop-kluster med hjälp av Hive-frågor](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive för SQL-användare lathund (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Undantagsmeddelanden|
|------------------------|
|Hive-skriptet är felaktigt.|


## <a name="error-0069"></a>Fel 0069  
 Undantag inträffar om det angivna SQL-skriptet inte är korrekt.  

 Det här felet i Azure Machine Learning uppstår om det angivna SQL-skriptet har syntaxproblem eller om kolumnerna eller tabellen som anges i skriptet inte är giltig. 

 Det här felet visas om SQL-motorn stöter på något fel när du kör frågan eller skriptet. SQL-felmeddelandet rapporteras normalt tillbaka i felloggen så att du kan vidta åtgärder baserat på det specifika felet.  

**Upplösning:** Gå tillbaka till modulen och kontrollera SQL-frågan för misstag.  

 Kontrollera att frågan fungerar korrekt utanför Azure ML genom att logga in på databasservern direkt och köra frågan.  

 Om det finns ett SQL-genererat meddelande som rapporteras av modulen undantag, vidta åtgärder baserat på det rapporterade felet. Felmeddelandena innehåller till exempel ibland specifik vägledning om det troliga felet:
+ *Ingen sådan kolumn eller en sådan databas som*anger att du kan ha skrivit fel kolumnnamn. Om du är säker på att kolumnnamnet är korrekt kan du prova att använda hakparenteser eller citattecken för att omge kolumnidentifieraren.
+ *SQL-logikfel \<nära\>SQL-nyckelordet*, vilket indikerar att du kan ha ett syntaxfel före det angivna nyckelordet

  
|Undantagsmeddelanden|
|------------------------|
|SQL-skriptet är felaktigt.|
|SQL-frågan {sql_query}" är inte korrekt.|
|SQL-frågan {sql_query}" är inte korrekt. Undantagsmeddelande: {exception}.|


## <a name="error-0070"></a>Fel 0070  
 Undantag inträffar när du försöker komma åt icke-existerande Azure-tabell.  

 Det här felet i Azure Machine Learning uppstår när du försöker komma åt en azure-tabell som inte finns. Det här felet visas om du anger en tabell i Azure-lagring, som inte finns när du läser från eller skriver till Azure Table Storage. Detta kan inträffa om du skriver fel namnet på den önskade tabellen, eller om du har en obalans mellan målnamnet och lagringstypen. Du har till exempel tänkt att läsa från en tabell men angav namnet på en blob i stället.  

**Upplösning:** Gå tillbaka till modulen för att kontrollera att namnet på tabellen är korrekt.  

|Undantagsmeddelanden|
|------------------------|
|Azure-tabellen finns inte.|
|Azure-tabellen {table_name}finns inte.|


## <a name="error-0072"></a>Fel 0072  
 Undantag inträffar vid timeout för anslutning.  

 Det här felet i Azure Machine Learning uppstår när en anslutning time out. Det här felet visas om det för närvarande finns anslutningsproblem med datakällan eller målet, till exempel långsam internetanslutning, eller om datauppsättningen är stor och/eller SQL-frågan som ska läsas i data utför komplicerad bearbetning.  

**Upplösning:** Ta reda på om det för närvarande finns problem med långsamma anslutningar till Azure-lagring eller internet.  

|Undantagsmeddelanden|
|------------------------|
|Tidsgränsen för anslutningen inträffade.|


## <a name="error-0073"></a>Fel 0073  
 Undantag uppstår om ett fel uppstår när en kolumn konverteras till en annan typ.  

 Det här felet i Azure Machine Learning uppstår när det inte är möjligt att konvertera kolumn till en annan typ.  Det här felet visas om en modul kräver en viss typ och det inte går att konvertera kolumnen till den nya typen.  

**Upplösning:** Ändra indatauppsättningen så att kolumnen kan konverteras baserat på det inre undantaget.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att konvertera kolumnen.|
|Det gick inte att konvertera kolumnen till {target_type}.|


## <a name="error-0075"></a>Fel 0075  
Undantag inträffar när en ogiltig binningsfunktion används vid kvantifiering av en datauppsättning.  

Det här felet i Azure Machine Learning uppstår när du försöker lagerplats data med hjälp av en metod som inte stöds, eller när parameterkombinationerna är ogiltiga.  

**Upplösning:**

Felhantering för den här händelsen introducerades i en tidigare version av Azure Machine Learning som tillät mer anpassning av binningmetoder. För närvarande baseras alla binningmetoder på ett urval från en listruta, så tekniskt sett bör det inte längre vara möjligt att få det här felet.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|Undantagsmeddelanden|
|------------------------|
|Ogiltig binningsfunktion som används.|


## <a name="error-0077"></a>Fel 0077  
 Undantag inträffar när okänt blob-filskrivningsläge har passerats.  

 Det här felet i Azure Machine Learning uppstår om ett ogiltigt argument skickas i specifikationerna för ett blob-filmål eller en källa.  

**Upplösning:** I nästan alla moduler som importerar eller exporterar data till och från Azure blob storage tilldelas parametervärden som styr skrivläget med hjälp av en listruta. Därför är det inte möjligt att skicka ett ogiltigt värde, och det här felet bör inte visas. Det här felet kommer att inaktuellt i en senare version.  

|Undantagsmeddelanden|
|------------------------|
|Skrivningsläge som inte stöds.|
|Skrivningsläge som inte stöds: {blob_write_mode}.|


## <a name="error-0078"></a>Fel 0078  
 Undantag inträffar när HTTP-alternativet för [importdata](import-data.md) tar emot en 3xx-statuskod som anger omdirigering.  

 Det här felet i Azure Machine Learning uppstår när HTTP-alternativet för [importdata](import-data.md) tar emot en 3xx (301, 302, 304, etc.) statuskod som anger omdirigering. Det här felet visas om du försöker ansluta till en HTTP-källa som omdirigerar webbläsaren till en annan sida. Av säkerhetsskäl är omdirigering av webbplatser inte tillåtet som datakällor för Azure Machine Learning.  

**Upplösning:** Om webbplatsen är en betrodd webbplats anger du den omdirigerade webbadressen direkt.  

|Undantagsmeddelanden|
|------------------------|
|Http-omdirigering är inte tillåtet.|


## <a name="error-0079"></a>Fel 0079  
 Undantag inträffar om Azure-lagringsbehållarens namn anges felaktigt.  

 Det här felet i Azure Machine Learning inträffar om Azure-lagringsbehållarens namn anges felaktigt. Det här felet visas om du inte har angett både behållar- och blob-namnet (fil) med hjälp av alternativet **Sökväg till blob som börjar med behållaralternativet** när du skriver till Azure Blob Storage.  

**Upplösning:** Gå tillbaka till modulen [Exportera data](export-data.md) och kontrollera att den angivna sökvägen till blobben innehåller både behållaren och filnamnet i formatet **behållaren/filnamnet**.  

|Undantagsmeddelanden|
|------------------------|
|Azure-lagringsbehållarens namn är felaktigt.|
|Azure-lagringsbehållarens namn {container_name}är felaktigt. ett behållarnamn för formatbehållaren/bloben förväntades.|


## <a name="error-0080"></a>Fel 0080  
 Undantag inträffar när kolumn med alla värden som saknas inte tillåts av modulen.  

 Det här felet i Azure Machine Learning uppstår när en eller flera av kolumnerna som förbrukas av modulen innehåller alla värden som saknas. Om en modul till exempel beräknar aggregerad statistik för varje kolumn kan den inte fungera i en kolumn som inte innehåller några data. I sådana fall stoppas modulkörning med det här undantaget.  

**Upplösning:** Gå tillbaka till indatauppsättningen och ta bort alla kolumner som innehåller alla värden som saknas.  

|Undantagsmeddelanden|
|------------------------|
|Kolumner med alla värden som saknas är inte tillåtna.|
|Kolumnen {col_index_or_name} har alla värden som saknas.|


## <a name="error-0081"></a>Fel 0081  
 Undantag inträffar i PCA-modulen om antalet dimensioner som ska minskas till är lika med antalet funktionskolumner i indatauppsättningen, som innehåller minst en gles funktionskolumn.  

 Det här felet i Azure Machine Learning uppstår om följande villkor är uppfyllda: (a) indatauppsättningen har minst en gles kolumn och (b) det slutliga antalet dimensioner som begärs är detsamma som antalet indatadimensioner.  

**Upplösning:** Överväg att minska antalet dimensioner i utdata till att vara färre än antalet dimensioner i indata. Detta är typiskt i tillämpningar av PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Undantagsmeddelanden|
|------------------------|
|För datauppsättning som innehåller glesa funktionskolumner bör antalet dimensioner som ska minskas till vara mindre än antalet funktionskolumner.|


## <a name="error-0082"></a>Fel 0082  
 Undantag inträffar när en modell inte kan avserialiseras.  

 Det här felet i Azure Machine Learning uppstår när en sparad maskininlärningsmodell eller transformering inte kan läsas in av en nyare version av Azure Machine Learning-körningen som ett resultat av en brytningsändring.  

**Upplösning:** Den träningspipeline som producerade modellen eller transformeringen måste köras om och modellen eller transformeringen måste göras om.  

|Undantagsmeddelanden|
|------------------------|
|Modellen kunde inte deserialiseras eftersom den troligen serialiseras med ett äldre serialiseringsformat. Vänligen träna och reta modellen.|


## <a name="error-0083"></a>Fel 0083  
 Undantag inträffar om datauppsättning som används för utbildning inte kan användas för betongtyp av elev.  

 Det här felet i Azure Machine Learning uppstår när datauppsättningen är inkompatibel med eleven som tränas. Datauppsättningen kan till exempel innehålla minst ett värde som saknas på varje rad, och därför hoppas hela datauppsättningen över under träningen. I andra fall förväntar sig vissa maskininlärningsalgoritmer, till exempel avvikelseidentifiering, inte att etiketter ska finnas och kan utlösa det här undantaget om etiketter finns i datauppsättningen.  

**Upplösning:** Läs dokumentationen för den elev som används för att kontrollera kraven för indatauppsättningen. Undersök kolumnerna för att se att alla nödvändiga kolumner finns.  

|Undantagsmeddelanden|
|------------------------|
|Datauppsättning som används för utbildning är ogiltig.|
|{data_name} innehåller ogiltiga data för utbildning.|
|{data_name} innehåller ogiltiga data för utbildning. Elevtyp: {learner_type}.|
|{data_name} innehåller ogiltiga data för utbildning. Elevtyp: {learner_type}. Orsak: {orsak}.|
|Det gick inte att tillämpa åtgärden {action_name}" på utbildningsdata {data_name}. Orsak: {orsak}.|


## <a name="error-0084"></a>Fel 0084  
 Undantag inträffar när poäng som produceras från ett R-skript utvärderas. Detta stöds för närvarande inte.  

 Det här felet i Azure Machine Learning uppstår om du försöker använda en av modulerna för att utvärdera en modell med utdata från ett R-skript som innehåller poäng.  

**Upplösning:**

|Undantagsmeddelanden|
|------------------------|
|Utvärdering av poäng som produceras av anpassad modell stöds för närvarande inte.|


## <a name="error-0085"></a>Fel 0085  
 Undantag inträffar när skriptutvärderingen misslyckas med ett fel.  

 Det här felet i Azure Machine Learning uppstår när du kör anpassade skript som innehåller syntaxfel.  

**Upplösning:** Granska koden i en extern redigerare och sök efter fel.  

|Undantagsmeddelanden|
|------------------------|
|Fel vid utvärdering av skript.|
|Följande fel uppstod under skriptutvärderingen, se utdataloggen för mer information:<br />---------- Felmeddelande från {script_language} tolk ----------<br />{meddelande}<br />---------- Felslut från {script_language} tolk ----------|


## <a name="error-0090"></a>Fel 0090  
 Undantag inträffar när skapandet av Hive-tabellen misslyckas.  

 Det här felet i Azure Machine Learning uppstår när du använder [Exportera data](export-data.md) eller ett annat alternativ för att spara data i ett HDInsight-kluster och den angivna Hive-tabellen kan inte skapas.  

**Upplösning:** Kontrollera Azure-lagringskontonamnet som är associerat med klustret och kontrollera att du använder samma konto i modulegenskaperna.  

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att skapa tabellen Hive. För ett HDInsight-kluster kontrollerar du att Azure-lagringskontonamnet som är associerat med klustret är detsamma som det som skickas in via modulparametern.|
|Det gick inte att skapa tabellen Hive {table_name}. För ett HDInsight-kluster kontrollerar du att Azure-lagringskontonamnet som är associerat med klustret är detsamma som det som skickas in via modulparametern.|
|Det gick inte att skapa tabellen Hive {table_name}. För ett HDInsight-kluster kontrollerar du att Azure-lagringskontonamnet som är associerat med klustret är {cluster_name}.For a HDInsight cluster, please ensure the Azure storage account name associated with cluster is {cluster_name}".|


## <a name="error-0102"></a>Fel 0102  
 Kastas när en ZIP-fil inte kan extraheras.  

 Det här felet i Azure Machine Learning uppstår när du importerar ett zippat paket med ZIP-tillägget, men paketet är antingen inte en zip-fil eller så använder filen inte ett zip-format som stöds.  

**Upplösning:** Kontrollera att den markerade filen är en giltig ZIP-fil och att den komprimerades med hjälp av en av komprimeringsalgoritmerna som stöds.  

 Om du får det här felet när du importerar datauppsättningar i komprimerat format kontrollerar du att alla filer som innehåller filanvändningen använder något av de filformat som stöds och är i Unicode-format.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Försök att läsa önskade filer till en ny komprimerad zippad mapp och försök att lägga till den anpassade modulen igen.  

|Undantagsmeddelanden|
|------------------------|
|Given ZIP-fil är inte i rätt format.|


## <a name="error-0105"></a>Fel 0105  
 Det här felet visas när en moduldefinitionsfil innehåller en parametertyp som inte stöds  
  
 Det här felet i Azure Machine Learning uppstår när du skapar en anpassad modul xml-definition och typen av en parameter eller ett argument i definitionen inte matchar en typ som stöds.  
  
**Upplösning:** Kontrollera att typegenskapen för ett **Arg-element** i xml-definitionsfilen för anpassad modul är en typ som stöds.  
  
|Undantagsmeddelanden|  
|------------------------|  
|Parametertyp som inte stöds.|  
|Parametertypen '{0}' som inte stöds har angetts.|  


## <a name="error-0107"></a>Fel 0107  
 Genereras när en moduldefinitionsfil definierar en utdatatyp som inte stöds  
  
 Det här felet i Azure Machine Learning uppstår när typen av en utdataport i en anpassad modul xml-definition inte matchar en typ som stöds.  
  
**Upplösning:** Kontrollera att typegenskapen för ett utdataelement i xml-definitionsfilen för anpassad modul är en typ som stöds.  
  
|Undantagsmeddelanden|  
|------------------------|  
|Utdatatyp som inte stöds.|  
|Utdatatypen {output_type} som inte stöds har angetts.|  


## <a name="error-0125"></a>Fel 0125  
 Genereras när schemat för flera datauppsättningar inte matchar.  

**Upplösning:**

|Undantagsmeddelanden|
|------------------------|
|Datauppsättningsschemat matchar inte.|


## <a name="error-0127"></a>Fel 0127  
 Bildpixelstorlek överskrider tillåten gräns  

 Det här felet uppstår om du läser bilder från en bilddatauppsättning för klassificering och bilderna är större än vad modellen kan hantera.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Undantagsmeddelanden|
|------------------------|
|Bildpixelstorleken överskrider tillåten gräns.|
|Bildpixelstorleken i filen {file_path} överskrider den tillåtna gränsen: {size_limit}.|


## <a name="error-0128"></a>Fel 0128  
 Antalet villkorade sannolikheter för kategoriska kolumner överskrider gränsen.  

**Upplösning:**

|Undantagsmeddelanden|
|------------------------|
|Antalet villkorade sannolikheter för kategoriska kolumner överskrider gränsen.|
|Antalet villkorade sannolikheter för kategoriska kolumner överskrider gränsen. Kolumnerna {column_name_or_index_1} och {column_name_or_index_2} är det problematiska paret.|


## <a name="error-0129"></a>Fel 0129  
 Antalet kolumner i datauppsättningen överskrider tillåten gräns.  

**Upplösning:**

|Undantagsmeddelanden|
|------------------------|
|Antalet kolumner i datauppsättningen överskrider tillåten gräns.|
|Antalet kolumner i datauppsättningen i {dataset_name} överskrider tillåtet.|
|Antalet kolumner i datauppsättningen i {dataset_name} överskrider den tillåtna gränsen för {component_name}.|
|Antalet kolumner i datauppsättningen i {dataset_name} överskrider tillåtna {limit_columns_count}-gränsen för {component_name}.|


## <a name="error-0134"></a>Fel 0134
Undantag inträffar när etikettkolumnen saknas eller har otillräckligt antal märkta rader.  

Det här felet uppstår när modulen kräver en etikettkolumn, men du inte tog med någon i kolumnmarkeringen eller etikettkolumnen saknar för många värden.

Det här felet kan också uppstå när en tidigare åtgärd ändrar datauppsättningen så att otillräckliga rader är tillgängliga för en nedströmsåtgärd. Anta till exempel att du använder ett uttryck i modulen **Partition och Exempel** för att dela upp en datauppsättning efter värden. Om inga matchningar hittas för ditt uttryck är en av de datauppsättningar som härrör från partitionen tom.

Lösning: 

 Om du inkluderar en etikettkolumn i kolumnmarkeringen men den inte känns igen använder du modulen [Redigera metadata](edit-metadata.md) för att markera den som en etikettkolumn.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Sedan kan du använda modulen [Rensa data som saknas](clean-missing-data.md) för att ta bort rader med saknade värden i etikettkolumnen. 

 Kontrollera indatauppsättningarna för att se till att de innehåller giltiga data och tillräckligt med rader för att uppfylla kraven för åtgärden. Många algoritmer genererar ett felmeddelande om de kräver några minsta antal rader med data, men data innehåller bara ett fåtal rader, eller bara ett huvud.

|Undantagsmeddelanden|
|------------------------|
|Undantag inträffar när etikettkolumnen saknas eller har otillräckligt antal märkta rader.|
|Undantag inträffar när etikettkolumnen saknas eller har mindre än {required_rows_count} märkta rader.|
|Undantag inträffar när etikettkolumnen i datauppsättningen {dataset_name} saknas eller har mindre än {required_rows_count} märkta rader.|


## <a name="error-0138"></a>Fel 0138  
 Minnet har uttömts, det går inte att slutföra körningen av modulen. Nedsampling av datauppsättningen kan bidra till att lösa problemet.  

 Det här felet uppstår när modulen som körs kräver mer minne än vad som är tillgängligt i Azure-behållaren. Detta kan inträffa om du arbetar med en stor datauppsättning och den aktuella åtgärden inte får plats i minnet.  

**Upplösning:** Om du försöker läsa en stor datauppsättning och åtgärden inte kan slutföras kan du hjälpa att förenkla datauppsättningen.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Undantagsmeddelanden|
|------------------------|
|Minnet har uttömts, det går inte att slutföra körningen av modulen.|
|Minnet har uttömts, det går inte att slutföra körningen av modulen. Detaljer: {details}|


## <a name="error-0141"></a>Fel 0141  
 Undantag inträffar om antalet markerade numeriska kolumner och unika värden i kategoriska kolumner och strängkolumner är för litet.  

 Det här felet i Azure Machine Learning uppstår när det inte finns tillräckligt med unika värden i den valda kolumnen för att utföra åtgärden.  

**Upplösning:** Vissa åtgärder utför statistiska åtgärder på funktionen och kategoriska kolumner, och om det inte finns tillräckligt med värden kan åtgärden misslyckas eller returnera ett ogiltigt resultat. Kontrollera din datauppsättning för att se hur många värden det finns i funktions- och etikettkolumnerna och ta reda på om åtgärden du försöker utföra är statistiskt giltig.  

 Om källdatauppsättningen är giltig kan du också kontrollera om vissa uppströms datamanipulering eller metadataåtgärd har ändrat data och tagit bort vissa värden.  

 Om uppströmsoperationer omfattar delning, sampling eller omsampling kontrollerar du att utdata innehåller det förväntade antalet rader och värden.  

|Undantagsmeddelanden|
|------------------------|
|Antalet markerade numeriska kolumner och unika värden i kategorisiska kolumner och strängkolumner är för litet.|
|Det totala antalet markerade numeriska kolumner och unika värden i kategoriska kolumner och strängkolumner (för närvarande {actual_num}) ska vara minst {lower_boundary}.|


## <a name="error-0154"></a>Fel 0154  
 Undantag inträffar när användaren försöker koppla data på nyckelkolumner med inkompatibel kolumntyp.

|Undantagsmeddelanden|
|------------------------|
|Elementtyperna nyckelkolumn är inte kompatibla.|
|Elementtyperna nyckelkolumn är inte kompatibla. (vänster: {keys_left}; höger: {keys_right})|


## <a name="error-0155"></a>Fel 0155  
 Undantag inträffar när kolumnnamn för datauppsättning inte är sträng.

|Undantagsmeddelanden|
|------------------------|
|Datarframekolumnnamnet måste vara strängtyp. Kolumnnamn är inte sträng.|
|Datarframekolumnnamnet måste vara strängtyp. Kolumnnamnen {column_names} är inte sträng.|


## <a name="error-0156"></a>Fel 0156  
 Undantag inträffar när det inte gick att läsa data från Azure SQL Database.

|Undantagsmeddelanden|
|------------------------|
|Det gick inte att läsa data från Azure SQL Database.|
|Det gick inte att läsa data från Azure SQL Database: {detailed_message} DB: {database_server_name}:{database_name} Fråga: {sql_statement}|


## <a name="error-0157"></a>Fel 0157  
 Det gick inte att hitta datalager.

|Undantagsmeddelanden|
|------------------------|
|Datastore-information är ogiltig.|
|Datastore-information är ogiltig. Det gick inte att hämta AzureML-datalager {datastore_name} på arbetsytan {workspace_name}.|


## <a name="error-0158"></a>Fel 0158
 Genereras när en omvandlingskatalog är ogiltig.

|Undantagsmeddelanden|
|------------------------------------------------------------|
|Given TransformationDirectory är ogiltig.|
|TransformationDirectory {arg_name}är ogiltig. Orsak: {orsak}. Kör utbildningsexperiment igen som genererar transformeringsfilen. Om träningsexperimentet har tagits bort återskapas och sparas transformeringsfilen.|


## <a name="error-1000"></a>Fel 1000  
Undantag för internt bibliotek.  

Det här felet tillhandahålls för att fånga annars ohanterade interna motorfel. Orsaken till det här felet kan därför vara olika beroende på vilken modul som genererade felet.  

För att få mer hjälp rekommenderar vi att du publicerar det detaljerade meddelandet som medföljer felet till Azure Machine Learning-forumet, tillsammans med en beskrivning av scenariot, inklusive de data som används som indata. Denna feedback hjälper oss att prioritera fel och identifiera de viktigaste frågorna för vidare arbete.  

|Undantagsmeddelanden|
|------------------------|
|Undantag för bibliotek.|
|Undantag för bibliotek: {exception}.|
|Okänt biblioteksundantag: {exception}. {customer_support_guidance}.|

