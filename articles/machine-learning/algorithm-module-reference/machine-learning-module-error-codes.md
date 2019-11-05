---
title: Felsöka fel i moduler
titleSuffix: Azure Machine Learning
description: Felsöka undantag i moduler i Azure Machine Learning Studio med felkoder
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b07b1fc14ac7ac72a30f75a098b4aab35f2de713
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497732"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Undantag och felkoder för algoritmen & module-referens

Lär dig mer om fel meddelanden och undantags koder som du kan stöta på med moduler i Azure Machine Learning designer (för hands version). 

Lös problemet genom att leta efter felet i den här artikeln för att läsa om vanliga orsaker. Det finns två sätt att hämta den fullständiga texten i ett fel meddelande i designern:  
 
- Klicka på länken, **Visa utgående logg**, i den högra rutan och rulla längst ned. Det detaljerade fel meddelandet visas på de två sista raderna i fönstret.  
  
- Välj den modul där felet finns och klicka på det röda X. Endast den relevanta fel texten visas.  
  
Om fel meddelande texten inte är användbar kan du skicka oss information om kontexten och eventuella tillägg eller ändringar genom att skicka feedback.


## <a name="error-0001"></a>Fel 0001  
 Undantag uppstår om en eller flera angivna kolumner med data uppsättningen inte kunde hittas.  
  
 Du får det här felet om ett kolumn val görs för en modul, men den eller de markerade kolumnerna finns inte i inmatnings data uppsättningen. Det här felet kan inträffa om du har angett ett kolumn namn manuellt eller om kolumn väljaren har angett en föreslagen kolumn som inte fanns i data uppsättningen när du körde pipelinen.  
  
**Lösning:** Gå tillbaka i modulen för att lösa detta undantag och kontrol lera att kolumn namn eller namn är korrekta och att alla refererade kolumner finns.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att hitta en eller flera angivna kolumner|  
|Det gick inte att hitta kolumnen med namnet eller indexet{0}|  
|Kolumnen med namnet eller indexet{0}finns inte i{1}|  
 

## <a name="error-0002"></a>Fel 0002  
 Undantag uppstår om en eller flera parametrar inte kunde parsas eller konverteras från en angiven typ till obligatorisk av typ av mål.  
  
 Det här felet uppstår i Azure Machine Learning när du anger en parameter som inmatad och värdetyp skiljer sig från den förväntade typen, och implicit konvertering kan inte utföras.  
  
**Lösning:** Kontrol lera modulens krav och Bestäm vilken Värdetyp som krävs (sträng, heltal, dubbelt osv.)  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att parsa parametern|  
|Det gick inte att parsa parametern{0}|  
|Det gick inte att parsa (konvertera) "{0}"-parametern till "{1}"|  
|Det gick inte att konvertera parametern{0}från{1}till{2}|  
|Det gick inte att konvertera värdet för parametern{0}"{1}" från "{2}" till "{3}"|  
|Det gick inte att konvertera värdet "{0}" i kolumnen "{1}" från "{2}" till "{3}" med användningen av formatet "{4}" som anges|  
  

## <a name="error-0003"></a>Fel 0003  
 Undantag uppstår om en eller flera indata är null eller tomma.  
  
 Du får det här felet i Azure Machine Learning om några indata eller parametrar till en modul är null eller tomma.  Det här felet kan inträffa, till exempel när du inte har angett något värde för en parameter. Det kan också inträffa om du väljer en data uppsättning som saknar värden eller en tom data uppsättning.  
  
**Lösning**
 
+ Öppna den modul som skapade undantaget och kontrol lera att alla indata har angetts. Se till att alla nödvändiga indata har angetts. 
+ Se till att data som läses in från Azure Storage är tillgängliga och att konto namnet eller nyckeln inte har ändrats.  
+ Kontrol lera indata för saknade värden eller null-värden.
+ Om du använder en fråga på en data källa kontrollerar du att data returneras i det format som du förväntar dig. 
+ Kontrol lera om det finns skrivfel eller andra ändringar i data specifikationen.
  
|Undantags meddelanden|  
|------------------------|  
|En eller flera indata är null eller tomma|  
|Inmatade{0}är null eller tomt|  
  

## <a name="error-0004"></a>Fel 0004  
 Undantaget inträffar om parametern är mindre än eller lika med det angivna värdet.  
  
 Du får det här felet i Azure Machine Learning om parametern i meddelandet är under ett begränsat värde som krävs för att modulen ska bearbeta data.  
  
**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den är större än det angivna värdet.  
  
|Undantags meddelanden|  
|------------------------|  
|Parametern måste vara större än det giltiga värdet.|  
|Värdet för parametern "{0}" måste vara större än {1}.|  
|Parametern "{0}" har värdet "{1}" som ska vara större än {2}|  
  


## <a name="error-0005"></a>Fel 0005  
 Undantaget inträffar om parametern är mindre än ett angivet värde.  
  
 Du får det här felet i Azure Machine Learning om parametern i meddelandet är under eller lika med ett begränsat värde som krävs för att modulen ska bearbeta data.  
  
**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den är större än eller lika med det angivna värdet.  
  
|Undantags meddelanden|  
|------------------------|  
|Parametern måste vara större än eller lika med ett begränsat värde.|  
|Värdet för parametern "{0}" måste vara större än eller lika med {1}.|  
|Parametern "{0}" har värdet "{1}" som ska vara större än eller lika med {2}.|  
  

## <a name="error-0006"></a>Fel 0006  
 Undantaget inträffar om parametern är större än eller lika med det angivna värdet.  
  
 Du får det här felet i Azure Machine Learning om parametern i meddelandet är större än eller lika med ett begränsat värde som krävs för att modulen ska bearbeta data.  
  
**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den är mindre än det angivna värdet.  
  
|Undantags meddelanden|  
|------------------------|  
|Parameter matchnings fel. En av parametrarna måste vara mindre än en annan.|  
|Värdet för parametern "{0}" måste vara mindre än värdet för parametern "{1}".|  
|Parametern{0}har värdet{1}som ska vara mindre än {2}.|  
  

## <a name="error-0007"></a>Fel 0007  
 Ett undantags fel uppstår om parametern är större än ett angivet värde.  
  
 Du får det här felet i Azure Machine Learning om du i egenskaperna för modulen har angett ett värde som är större än vad som tillåts. Du kan till exempel ange en data som ligger utanför intervallet för de datum som stöds, eller så kan du ange att fem kolumner ska användas när endast tre kolumner är tillgängliga. 
 
 Du kan också se det här felet om du anger två uppsättningar med data som behöver matcha på något sätt. Om du till exempel byter namn på kolumner och anger kolumnerna efter index, måste antalet namn som du anger matcha antalet kolumn index. Ett annat exempel kan vara en matematik åtgärd som använder två kolumner, där kolumnerna måste ha samma antal rader. 
  
**Lösning**
 
 + Öppna modulen i fråga och granska eventuella numeriska egenskaps inställningar.
 + Se till att alla parameter värden ligger inom det tillåtna värde intervallet för den egenskapen.
 + Om modulen kräver flera indata kontrollerar du att indata är av samma storlek.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Kontrol lera om data uppsättningen eller data källan har ändrats. Ibland Miss kan ett värde som fungerade med en tidigare version av data efter antalet kolumner, kolumnens data typer eller data storleken ändrats.  
  
|Undantags meddelanden|  
|------------------------|  
|Parameter matchnings fel. En av parametrarna måste vara mindre än eller lika med en annan.|  
|Värdet för parametern "{0}" måste vara mindre än eller lika med värdet för parametern "{1}".|  
|Parametern "{0}" har värdet "{1}" som ska vara mindre än eller lika med {2}.|  
  

## <a name="error-0008"></a>Fel 0008  
 Undantaget inträffar om parametern inte är i intervallet.  
  
 Du får det här felet i Azure Machine Learning om parametern i meddelandet ligger utanför de gränser som krävs för att modulen ska bearbeta data.  
  
 Det här felet visas till exempel om du försöker använda [Lägg till rader](add-rows.md) för att kombinera två data uppsättningar som har ett annat antal kolumner.  
  
**Lösning:** Gå tillbaka i modulen som utlöser undantaget och ändra parametern så att den ligger inom det angivna intervallet.  
  
|Undantags meddelanden|  
|------------------------|  
|Parametervärdet är inte inom det angivna intervallet.|  
|Värdet för parametern "{0}" är inte inom intervallet.|  
|Värdet för parametern{0}måste ligga i intervallet [{1}, {2}].|  
  

## <a name="error-0009"></a>Fel 0009  
 Undantag inträffar när namnet på Azure Storage-kontot eller container namnet har angetts felaktigt.  
  
Det här felet uppstår i Azure Machine Learning designer när du anger parametrar för ett Azure Storage-konto, men det går inte att matcha namnet eller lösen ordet. Fel på lösen ord eller konto namn kan bero på många orsaker:
 
 + Kontot har fel typ. Vissa nya konto typer stöds inte för användning med Machine Learning designer. Mer information finns i [Importera data](import-data.md) .
 + Du har angett ett felaktigt konto namn
 + Kontot finns inte längre
 + Lösen ordet för lagrings kontot är felaktigt eller har ändrats
 + Du angav inte behållar namnet, eller så finns inte behållaren
 + Du angav inte fullständigt sökvägen till filen (sökväg till blobben)
   
**Lösning**

Sådana problem uppstår ofta när du försöker ange konto namn, lösen ord eller container Sök väg manuellt. Vi rekommenderar att du använder den nya guiden för modulen [Importera data](import-data.md) som hjälper dig att söka efter och kontrol lera namn.

Kontrol lera också om kontot, behållaren eller blobben har tagits bort. Använd ett annat Azure Storage-verktyg för att kontrol lera att konto namnet och lösen ordet har angetts korrekt och att behållaren finns. 

Vissa nyare konto typer stöds inte av Azure Machine Learning. Till exempel kan de nya lagrings typerna "frekvent" eller "kall" inte användas för Machine Learning. Både klassiska lagrings konton och lagrings konton som skapats som "allmänna ändamål" fungerar bra.

Om den fullständiga sökvägen till en BLOB har angetts kontrollerar du att sökvägen har angetts som **container/blobname**och att både behållaren och bloben finns i kontot.  
  
 Sökvägen får inte innehålla något inledande snedstreck. Till exempel är fel **/container/BLOB** och ska anges som **container/BLOB**.  

  
|Undantags meddelanden|  
|------------------------|  
|Namnet på Azure Storage-kontot eller containern är felaktigt.|  
|Namnet på Azure Storage-kontot "{0}" eller container namnet "{1}" är felaktigt. ett behållar namn för format containern/blobben förväntades.|  
  

## <a name="error-0010"></a>Fel 0010  
 Undantag uppstår om indata-datauppsättningar har kolumn namn som ska matcha men inte.  
  
 Det här felet visas i Azure Machine Learning om kolumn indexet i meddelandet har olika kolumn namn i de två data uppsättningarna.  
  
**Lösning:** Använd [Redigera metadata](edit-metadata.md) eller ändra den ursprungliga data uppsättningen till samma kolumn namn för det angivna kolumn indexet.  
  
|Undantags meddelanden|  
|------------------------|  
|Kolumner med motsvarande index i indata-datauppsättningar har olika namn.|  
|Kolumn namnen är inte desamma för Column {0} (noll-baserad) för indata-datauppsättningar ({1} respektive {2}).|  
  

## <a name="error-0011"></a>Fel 0011  
 Undantag inträffar om det skickade kolumn uppsättnings argumentet inte gäller för någon av data mängds kolumnerna.  
  
 Det här felet visas i Azure Machine Learning om det angivna kolumn urvalet inte matchar någon av kolumnerna i den angivna data uppsättningen.  
  
 Du kan också få det här felet om du inte har valt en kolumn och minst en kolumn krävs för att modulen ska fungera.  
  
**Lösning:** Ändra kolumn valet i modulen så att den kommer att gälla för kolumnerna i data uppsättningen.  
  
 Om modulen kräver att du väljer en viss kolumn, till exempel en etikett kolumn, kontrollerar du att den högra kolumnen är markerad.  
  
 Om olämpliga kolumner är markerade tar du bort dem och kör pipelinen igen.  
  
|Undantags meddelanden|  
|------------------------|  
|Angiven kolumn uppsättning gäller inte för någon av data mängds kolumnerna.|  
|Den angivna kolumn uppsättningen{0}gäller inte för någon av data mängds kolumnerna.|  
  

## <a name="error-0012"></a>Fel 0012  
 Undantag uppstår om det inte gick att skapa en instans av klassen med den angivna argument uppsättningen.  
  
**Lösning:** Det här felet är inte åtgärds bara av användaren och kommer att bli inaktuell i en framtida version.  
  
|Undantags meddelanden|  
|------------------------|  
|Modell som inte är tränad, träna modell först.|  
|Modell som inte är tränad ({0}), Använd tränad modell.|  
  

## <a name="error-0013"></a>Fel 0013  
 Undantag uppstår om den som har skickats till modulen är en ogiltig typ.  
  
 Felet uppstår när en utbildad modell inte är kompatibel med den anslutna bedömnings modulen. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->
  
**Lösning**

Ta reda på vilken typ av elev som produceras av modulen utbildning och Bestäm vilken bedömnings modul som är lämplig för din elev. 

Om modellen tränas med någon av de specialiserade inlärnings modulerna ansluter du bara den tränade modellen till motsvarande anpassade bedömnings modell. 


|Modell typ|Modul för utbildning| Bedömnings modul|
|----|----|----|
|alla klassificerare|[Träna modell](train-model.md) |[Poäng modell](score-model.md)|
|Regressions modell|[Träna modell](train-model.md) |[Poäng modell](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Undantags meddelanden|  
|------------------------|  
|En elev av en ogiltig typ har skickats.|  
|Eleven "{0}" har en ogiltig typ.|  


## <a name="error-0014"></a>Fel 0014  
 Undantag uppstår om antalet unika kolumn värden är större än tillåtet.  
  
 Det här felet uppstår när en kolumn innehåller för många unika värden.  Du kan till exempel se det här felet om du anger att en kolumn ska hanteras som kategoriska data, men det finns för många unika värden i kolumnen som tillåter bearbetning. Du kan också se det här felet om det uppstår ett matchnings fel mellan antalet unika värden i två indata.   
  
**Lösning**

Öppna den modul som genererade felet och identifiera de kolumner som används som indata. För vissa moduler kan du högerklicka på data uppsättningen och välja **visualisera** för att hämta statistik för enskilda kolumner, inklusive antalet unika värden och deras distribution.

För kolumner som du tänker använda för gruppering eller kategorisering, vidta åtgärder för att minska antalet unika värden i kolumner. Du kan minska på olika sätt beroende på kolumnens datatyp. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Det går inte att hitta någon lösning som matchar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på den modul som orsakade felet och data typen och kolumnens kardinalitet. Vi kommer att använda informationen för att tillhandahålla mer riktade fel söknings steg för vanliga scenarier.   
  
|Undantags meddelanden|  
|------------------------|  
|Antalet unika kolumn värden är större än vad som tillåts.|  
|Antal unika värden i kolumnen: "{0}" överskrider antalet tupler {1}.|  
  

## <a name="error-0015"></a>Fel 0015  
 Undantag uppstår om databas anslutningen har misslyckats.  
  
 Du får det här felet om du anger ett felaktigt SQL-kontonamn, ett lösen ord, en databas server eller ett databas namn, eller om det inte går att upprätta en anslutning till databasen på grund av problem med databasen eller servern.  
  
**Lösning:** Kontrol lera att konto namnet, lösen ordet, databas servern och databasen har angetts korrekt och att det angivna kontot har rätt behörighets nivå. Kontrol lera att databasen är tillgänglig för närvarande.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att ansluta till databasen.|  
|Det gick inte att ansluta till databasen: {0}.|  
  


## <a name="error-0016"></a>Fel 0016  
 Undantag uppstår om indata-datauppsättningar som skickas till modulen ska ha kompatibla kolumn typer men inte.  
  
 Du får det här felet i Azure Machine Learning om kolumn typerna som skickas i två eller flera data uppsättningar inte är kompatibla med varandra.  
  
**Lösning:** Använd [Redigera metadata](edit-metadata.md) eller ändra den ursprungliga indata-datauppsättningen<!--, or use [Convert to Dataset](convert-to-dataset.md)--> för att säkerställa att kolumn typerna är kompatibla.  
  
|Undantags meddelanden|  
|------------------------|  
|Kolumner med motsvarande index i indata-datauppsättningar har inkompatibla typer.|  
|Kolumnerna {0} och {1} är inkompatibla.|  
|Kolumn element typerna är inte kompatibla för kolumn {0} (noll-baserad) för indata-datauppsättningar ({1} respektive {2}).|  
  

## <a name="error-0017"></a>Fel 0017  
 Undantag uppstår om en markerad kolumn använder en datatyp som inte stöds av den aktuella modulen.  
  
 Du kan till exempel få det här felet i Azure Machine Learning om ditt kolumn val innehåller en kolumn med en datatyp som inte kan bearbetas av modulen, till exempel en sträng kolumn för en matematik åtgärd, eller en score-kolumn där en kategoriska Feature-kolumn är kunna.  
  
**Lösning**
 1. Identifiera den kolumn som är problemet.
 2. Granska kraven för modulen.
 3. Ändra kolumnen så att den uppfyller kraven. Du kan behöva använda flera av följande moduler för att göra ändringar, beroende på kolumn och konvertering som du försöker:
    + Använd [Redigera metadata](edit-metadata.md) om du vill ändra data typen för kolumner eller ändra kolumn användningen från funktion till numerisk, kategoriska till icke-kategoriska, och så vidare.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Som sista utväg kan du behöva ändra den ursprungliga indata-datauppsättningen.

> [!TIP]
> Det går inte att hitta någon lösning som matchar ditt scenario? Du kan ge feedback om det här avsnittet som innehåller namnet på den modul som orsakade felet och data typen och kolumnens kardinalitet. Vi kommer att använda informationen för att tillhandahålla mer riktade fel söknings steg för vanliga scenarier. 
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att bearbeta kolumnen av den aktuella typen. Typen stöds inte av modulen.|  
|Det går inte att bearbeta kolumnen av typen {0}. Typen stöds inte av modulen.|  
|Det går inte att bearbeta kolumnen{1}av typen {0}. Typen stöds inte av modulen.|  
|Det går inte att bearbeta kolumnen{1}av typen {0}. Typen stöds inte av modulen. Parameter namn: {2}|  
  

## <a name="error-0018"></a>Fel 0018  
 Undantaget inträffar om indata-datauppsättningen är ogiltig.  
  
**Lösning:** Det här felet i Azure Machine Learning kan visas i många sammanhang, så det finns ingen enskild lösning. I allmänhet anger felet att data som angetts som indata till en modul har fel antal kolumner eller att data typen inte överensstämmer med kraven för modulen. Till exempel:  
  
-   Modulen kräver en etikett kolumn, men ingen kolumn har marker ATS som en etikett eller så har du inte valt en etikett kolumn ännu.  
  
-   Modulen kräver att data är kategoriska men dina data är numeriska.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Data har fel format.  
  
-   Importerade data innehåller ogiltiga tecken, felaktiga värden eller out-of-Range-värden.  
-   Kolumnen är tom eller innehåller för många värden som saknas.  
  
 För att fastställa kraven och hur dina data kan se igenom hjälp avsnittet för den modul som ska använda data uppsättningen som indata.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Undantags meddelanden|  
|------------------------|  
|Data uppsättningen är ogiltig.|  
|{0} innehåller ogiltiga data.|  
|{0} och {1} bör vara konsekvent kolumn.|  
  

## <a name="error-0019"></a>Fel 0019  
 Undantag uppstår om kolumnen förväntas innehålla sorterade värden, men inte.  
  
 Du får det här felet i Azure Machine Learning om de angivna kolumn värdena är i fel ordning.  
  
**Lösning:** Sortera kolumnvärdena genom att manuellt ändra data uppsättningen för indata och köra modulen igen.  
  
|Undantags meddelanden|  
|------------------------|  
|Värdena i kolumnen sorteras inte.|  
|Värdena i kolumnen{0}sorteras inte.|  
|Värdena i kolumnen{0}för data uppsättningen{1}sorteras inte.|  
  

## <a name="error-0020"></a>Fel 0020  
 Undantag uppstår om antalet kolumner i några av data uppsättningarna som skickats till modulen är för litet.  
  
 Du får det här felet i Azure Machine Learning om inte tillräckligt många kolumner har valts för en modul.  
  
**Lösning:** Gå tillbaka i modulen och se till att kolumn väljaren har rätt antal valda kolumner.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet.|  
|Antalet kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet för {0} kolumn (er).|  
|Antalet kolumner i indata-datamängden{0}är mindre än det tillåtna minimivärdet för {1} kolumn (er).|

## <a name="error-0021"></a>Fel 0021  
 Undantag uppstår om antalet rader i några av data uppsättningarna som skickats till modulen är för litet.  
  
 Det här felet visas i Azure Machine Learning när det inte finns tillräckligt många rader i data uppsättningen för att utföra den angivna åtgärden. Du kan till exempel se det här felet om indata-datauppsättningen är tom, eller om du försöker utföra en åtgärd som kräver att ett visst minsta antal rader är giltigt. Sådana åtgärder kan omfatta (men är inte begränsade till) gruppering eller klassificering baserat på statistiska metoder, vissa typer av diskretisering och inlärning med antal.  
  
**Lösning**
 
 + Öppna den modul som returnerade felet och kontrol lera indata-och modul egenskaper. 
 + Kontrol lera att indata-datauppsättningen inte är tom och att det finns tillräckligt med rader med data som uppfyller de krav som beskrivs i modulens hjälp.  
 + Om dina data har lästs in från en extern källa, se till att data källan är tillgänglig och att det inte finns något fel eller någon ändring i data definitionen som skulle göra att import processen får färre rader.
 + Om du utför en åtgärd på data strömmen för den modul som kan påverka typen av data eller antalet värden, t. ex. rensnings-, delnings-eller kopplings åtgärder, kontrollerar du utdata för dessa åtgärder för att avgöra hur många rader som returneras.  



## <a name="error-0022"></a>Fel 0022  
 Undantag uppstår om antalet markerade kolumner i indata-datamängd inte motsvarar det förväntade antalet.  
  
 Det här felet i Azure Machine Learning kan inträffa när den underordnade modulen eller åtgärden kräver ett särskilt antal kolumner eller indata, och du har angett för få eller för många kolumner eller indata. Till exempel:  
  
-   Du anger en enskild etikett kolumn eller nyckel kolumn och har valt flera kolumner av misstag.  
  
-   Du byter namn på kolumner, men tillhandahöll fler eller färre namn än det finns kolumner.  
  
-   Antalet kolumner i källan eller målet har ändrats eller stämmer inte överens med antalet kolumner som används av modulen.  
  
-   Du har angett en kommaavgränsad lista med värden för indata, men antalet värden matchar inte, eller så stöds inte flera indata.  
  
**Lösning:** Gå tillbaka i modulen och markera kolumn markeringen för att se till att rätt antal kolumner är markerat. Verifiera utdata från överordnade moduler och kraven för efterföljande åtgärder.  
  
 Om du använde något av alternativen för kolumn val som kan markera flera kolumner (kolumn index, alla funktioner, alla numeriska osv.), verifierar du det exakta antalet kolumner som returneras av markeringen.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Kontrol lera att de överordnade kolumnernas antal eller typ inte har ändrats.  
  
 Om du använder en rekommendations data uppsättning för att träna en modell måste du komma ihåg att rekommendationen förväntar sig ett begränsat antal kolumner, som motsvarar användar objekt par eller användar objekts rankning. Ta bort ytterligare kolumner innan du tränar data uppsättningarna modell eller data delnings rekommendation. Mer information finns i [dela data](split-data.md).  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet markerade kolumner i indata-datamängden är inte lika med det förväntade talet.|  
|Antalet markerade kolumner i indata-datamängden är inte lika med {0}.|  
|Kolumn markerings mönster "{0}" tillhandahåller antalet markerade kolumner i indata-dataset som inte är lika med {1}.|  
|Kolumn markerings mönster "{0}" förväntas tillhandahålla {1} kolumn (er) som har marker ATS i indata-datauppsättningen, men {2} kolumn (er) har angetts.|  



## <a name="error-0023"></a>Fel 0023  
 Undantag inträffar om mål kolumnen i data uppsättningen dataset inte är giltig för den aktuella utbildaren.  
  
 Det här felet uppstår i Azure Machine Learning om mål kolumnen (som valts i modulen Module) inte är av en giltig datatyp, innehåller alla saknade värden eller inte kategoriska som förväntat.  
  
**Lösning:** Gå tillbaka till modulens inmatade för att kontrol lera innehållet i kolumnen etikett/mål. Kontrol lera att det inte har alla värden som saknas. Om modulen förväntar sig att mål kolumnen ska vara kategoriska kontrollerar du att det finns fler än ett distinkt värde i kolumnen Target.  
  
|Undantags meddelanden|  
|------------------------|  
|Den angivna data uppsättningen innehåller en mål kolumn som inte stöds.|  
|Den angivna data uppsättningen innehåller en mål kolumn som inte stöds{0}.|  
|Den angivna data uppsättningen innehåller en mål kolumn som inte stöds{0}för att lära av typen {1}.|  
 

## <a name="error-0024"></a>Fel 0024  
Undantag uppstår om data uppsättningen inte innehåller en etikett kolumn.  

 Det här felet uppstår i Azure Machine Learning när modulen kräver en etikett kolumn och data uppsättningen inte har en etikett kolumn. Till exempel kräver utvärderingen av en resultat uppsättning vanligt vis att det finns en etikett kolumn för att beräkna noggrannhets mått.  
 
Det kan också hända att en etikett kolumn finns i data uppsättningen, men inte identifieras korrekt av Azure Machine Learning.
  
**Lösning**

+ Öppna den modul som genererade felet och ta reda på om en etikett kolumn finns. Namnet eller data typen för kolumnen spelar ingen roll, så länge kolumnen innehåller ett enda resultat (eller beroende variabel) som du försöker förutsäga. Om du inte är säker på vilken kolumn som har etiketten söker du efter ett generiskt namn, till exempel *klass* eller *mål*. 
+  Om data uppsättningen inte innehåller någon etikett kolumn, är det möjligt att kolumnen etikett har tagits bort direkt eller av misstag. Det kan också vara att data uppsättningen inte är utdata från en underordnad poängsättnings-modul.
+ Om du uttryckligen vill markera kolumnen som etikett-kolumn, lägger du till modulen [Redigera metadata](edit-metadata.md) och ansluter data uppsättningen. Markera endast kolumnen etikett och välj **etikett** i list rutan **fält** . 
+ Om fel kolumn har valts som etikett kan du välja **Rensa etikett** från **fälten** för att åtgärda metadata för kolumnen. 
  
|Undantags meddelanden|  
|------------------------|  
|Det finns ingen etikett kolumn i data uppsättningen.|  
|Det finns ingen etikett kolumn i "{0}".|  
  

## <a name="error-0025"></a>Fel 0,025  
 Undantag uppstår om data uppsättningen inte innehåller någon Scores-kolumn.  
  
 Det här felet uppstår i Azure Machine Learning om indatamängden till evaluate-modellen inte innehåller giltiga Poäng kolumner. Användaren försöker till exempel utvärdera en data uppsättning innan den beskrevs med en korrekt tränad modell, eller så släpptes kolumnen poäng direkt. Detta undantag uppstår även om poängen i de två data uppsättningarna är inkompatibla. Du kan till exempel försöka jämföra noggrannheten i en linjär modellerings regressor med en binär klassificerare.  
  
**Lösning:** Gå tillbaka till indatamängden för utvärdera modellen och Undersök om den innehåller en eller flera Poäng kolumner. Om inte, har data uppsättningen inte betyget eller så har inga poäng kolumner släppts i en överordnad modul.  
  
|Undantags meddelanden|  
|------------------------|  
|Det finns ingen Poäng kolumn i data uppsättningen.|  
|Det finns ingen Poäng kolumn i "{0}".|  
|Det finns ingen resultat kolumn i "{0}" som skapas av en "{1}". Poäng data uppsättningen med rätt typ av lära.|  
  

## <a name="error-0026"></a>Fel 0026  
 Undantag uppstår om kolumner med samma namn inte är tillåtna.  
  
 Det här felet i Azure Machine Learning uppstår om flera kolumner har samma namn. Ett sätt som du kan få det här felet är om data uppsättningen inte har någon rubrik rad och kolumn namn tilldelas automatiskt: Col0, Col1 osv.  
  
**Lösning:** Om kolumner har samma namn, infogar du en modul för att [Redigera metadata](edit-metadata.md) mellan indata-datauppsättningen och modulen. Använd kolumn väljaren i [Redigera metadata](edit-metadata.md) för att välja kolumner att byta namn på, skriva de nya namnen i text rutan **nya kolumn namn** .  
  
|Undantags meddelanden|  
|------------------------|  
|Samma kolumn namn anges i argument. Samma kolumn namn tillåts inte i modulen.|  
|Samma kolumn namn i argumenten "{0}" och "{1}" tillåts inte. Ange olika namn.|  
  

## <a name="error-0027"></a>Fel 0027  
 Undantag inträffar om två objekt måste ha samma storlek men inte.  
  
 Detta är ett vanligt fel i Azure Machine Learning och kan orsakas av många villkor.  
  
**Lösning:** Det finns ingen enskild lösning. Du kan dock söka efter villkor som följande:  
  
-   Om du byter namn på kolumner, se till att varje lista (kolumnerna och listan med nya namn) har samma antal objekt.  
  
-   Om du kopplar ihop eller sammanfogar två data uppsättningar, se till att de har samma schema.  
  
-   Om du ansluter två data uppsättningar som har flera kolumner måste du kontrol lera att nyckel kolumnerna har samma datatyp och välja alternativet **Tillåt dubbletter och bevara kolumn ordning i markeringen**.  
  
|Undantags meddelanden|  
|------------------------|  
|Storleken på överförda objekt är inkonsekvent.|  
|Storleken på{0}är inkonsekvent med storleken "{1}".|  
  

## <a name="error-0028"></a>Fel 0028  
 Ett undantag inträffar i fallet när kolumn uppsättningen innehåller dubbletter av kolumn namn och det inte är tillåtet.  
  
 Det här felet uppstår i Azure Machine Learning när kolumn namn är duplicerade; Detta är inte unikt.  
  
**Lösning:** Om några kolumner har samma namn, lägger du till en instans av [Redigera metadata](edit-metadata.md) mellan indata-datauppsättningen och modulen som upphöjer felet. Använd kolumn väljaren i [Redigera metadata](edit-metadata.md) för att välja kolumner att byta namn på och Skriv de nya kolumn namnen i text rutan **nya kolumn namn** . Om du byter namn på flera kolumner kontrollerar du att värdena som du skriver i de **nya kolumn namnen** är unika.  
  
|Undantags meddelanden|  
|------------------------|  
|Kolumn uppsättningen innehåller ett eller flera kolumn namn.|  
|Namnet "{0}" är dubbletter.|  
|Namnet "{0}" är dubblerat i "{1}".|  
  

## <a name="error-0029"></a>Fel 0029  
 Undantag inträffar i fall när ogiltig URI skickas.  
  
 Det här felet uppstår i Azure Machine Learning om ogiltig URI skickas.  Du får det här felet om något av följande villkor är uppfyllt:, eller.  
  
-   Den offentliga eller SAS-URI: n som tillhandahölls för Azure Blob Storage för läsning eller skrivning innehåller ett fel.  
  
-   Tids perioden för SAS har upphört att gälla.  
  
-   Webb adressen via HTTP-källan representerar en fil eller en loopback-URI.  
  
-   Webb adressen via HTTP innehåller en felaktigt formaterad URL.  
  
-   URL: en kan inte matchas av fjär källan.  
  
**Lösning:** Gå tillbaka i modulen och kontrol lera formatet för URI: n. Om data källan är en webb adress via HTTP kontrollerar du att den avsedda källan inte är en fil eller en loopback-URI (localhost).  
  
|Undantags meddelanden|  
|------------------------|  
|Ogiltig URI har skickats.|  
  

## <a name="error-0030"></a>Fel 0030  
 Undantag uppstår i fallet när det inte går att hämta en fil.  
  
 Detta undantag i Azure Machine Learning inträffar när det inte går att hämta en fil. Du får det här undantaget när ett försök att läsa från en HTTP-källa misslyckades efter tre (3) nya försök.  
  
**Lösning:** Kontrol lera att URI: n till HTTP-källan är korrekt och att platsen är tillgänglig via Internet.  
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att hämta en fil.|  
|Fel vid hämtning av filen: {0}.|  
  

## <a name="error-0031"></a>Fel 0031  
 Undantaget inträffar om antalet kolumner i kolumn uppsättningen är mindre än vad som behövs.  
  
 Det här felet uppstår i Azure Machine Learning om antalet valda kolumner är mindre än vad som behövs.  Du får det här felet om det minsta antalet kolumner som krävs inte är markerat.  
  
**Lösning:** Lägg till ytterligare kolumner i kolumn urvalet med hjälp av **kolumn väljaren**.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet kolumner i kolumn uppsättningen är mindre än vad som krävs.|  
|Du måste ange {0} kolumn (er). Det faktiska antalet angivna kolumner är {1}.|  

## <a name="error-0032"></a>Fel 0032  
 Undantag inträffar om argumentet inte är ett tal.  
  
 Du får det här felet i Azure Machine Learning om argumentet är Double eller NaN.  
  
**Lösning:** Ändra det angivna argumentet så att det använder ett giltigt värde.  
  
|Undantags meddelanden|  
|------------------------|  
|Argumentet är inte ett tal.|  
|"{0}" är inte ett tal.|  
  

## <a name="error-0033"></a>Fel 0033  
 Undantaget inträffar om argumentet är oändligt.  
  
 Det här felet uppstår i Azure Machine Learning om argumentet är oändligt. Du får det här felet om argumentet är antingen `double.NegativeInfinity` eller `double.PositiveInfinity`.  
  
**Lösning:** Ändra det angivna argumentet till ett giltigt värde.  
  
|Undantags meddelanden|  
|------------------------|  
|Argumentet måste vara ändligt.|  
|"{0}" är inte begränsad.|  
  

## <a name="error-0034"></a>Fel 0034  
 Undantag uppstår om det finns fler än en klassificering för ett angivet användar objekt par.  
  
 Det här felet i Azure Machine Learning uppstår i rekommendationen om ett användar objekt par har fler än ett klassificerings värde.  
  
**Lösning:** Se till att paret User-item har ett enda klassificerings värde.  
  
|Undantags meddelanden|  
|------------------------|  
|Det finns fler än en klassificering för värdena i data uppsättningen.|  
|Fler än en klassificering för användar {0} och objekt {1} i data tabellen klassificerings förutsägelse.|  
  

## <a name="error-0035"></a>Fel 0035  
 Undantag uppstår om inga funktioner har angetts för en angiven användare eller ett objekt.  
  
 Det här felet uppstår i Azure Machine Learning du försöker använda en rekommendations modell för att få en bedömning, men det går inte att hitta någon funktions vektor.  
  
**Lösning**

Matchbox-rekommendationen har vissa krav som måste uppfyllas när du använder antingen objekt funktioner eller användar funktioner.  Det här felet indikerar att en funktions vektor saknas för en användare eller ett objekt som du har angett som indata.  Du måste se till att en Vector av funktioner är tillgänglig i data för varje användare eller objekt.  
  
 Om du till exempel har tränat en rekommendations modell med funktioner som användarens ålder, plats eller inkomst, men nu vill skapa Poäng för nya användare som inte har setts under utbildningen, måste du tillhandahålla en viss uppsättning funktioner (dvs., ålder, plats och inkomst värden) för de nya användarna för att kunna göra lämpliga förutsägelser för dem. 
 
 Om du inte har några funktioner för dessa användare bör du överväga att skapa lämpliga funktioner i funktions teknik.  Om du till exempel inte har enskilda användares ålder eller inkomst värden kan du generera ungefärliga värden som ska användas för en grupp av användare. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Upplösningen är inte tillämplig för ditt ärende? Du är välkommen att skicka feedback om den här artikeln och ange information om scenariot, inklusive modulen och antalet rader i kolumnen. Vi kommer att använda den här informationen för att tillhandahålla mer detaljerade fel söknings steg i framtiden.
   
|Undantags meddelanden|  
|------------------------|  
|Inga funktioner har angetts för en obligatorisk användare eller ett objekt.|  
|Funktioner för {0} krävs men har inte angetts.|  
  

## <a name="error-0036"></a>Fel 0036  
 Undantag uppstår om flera funktions vektorer har angetts för en specifik användare eller ett objekt.  
  
 Det här felet uppstår i Azure Machine Learning om en funktions vektor har definierats mer än en gång.  
  
**Lösning:** Se till att funktions vektorn inte har definierats mer än en gång.  
  
|Undantags meddelanden|  
|------------------------|  
|Duplicera funktions definition för en användare eller ett objekt.|  
|Duplicerad funktions definition för {0}.|  
  

## <a name="error-0037"></a>Fel 0037  
 Undantag uppstår om flera etikett kolumner har angetts och bara ett tillåts.  
  
 Det här felet uppstår i Azure Machine Learning om fler än en kolumn har valts som den nya etikett kolumnen. De flesta övervakade Learning-algoritmer kräver att en kolumn markeras som mål eller etikett.  
  
**Lösning:** Se till att välja en enskild kolumn som den nya etikett kolumnen.  
  
|Undantags meddelanden|  
|------------------------|  
|Flera etikett kolumner har angetts.|  
  

## <a name="error-0038"></a>Fel 0038  
 Undantaget inträffar om antalet element som förväntas ska vara ett exakt värde, men inte.  
  
 Det här felet uppstår i Azure Machine Learning om antalet förväntade element ska vara ett exakt värde, men inte.  Det här felet visas om antalet element inte är lika med det giltiga förväntade värdet.  
  
**Lösning:** Ändra inmatarna så att de har rätt antal element.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet element är inte giltigt.|  
|Antalet element i{0}är inte giltigt.|  
|Antalet element i{0}är inte lika med giltigt antal {1} element.|  
  

## <a name="error-0039"></a>Fel 0039  
 Ett undantag uppstår om en åtgärd har misslyckats.  
  
 Det här felet uppstår i Azure Machine Learning när en intern åtgärd inte kan slutföras.  
  
**Lösning:** Det här felet orsakas av många villkor och det finns ingen speciell påföljd.  
 Följande tabell innehåller allmänna meddelanden för det här felet, som följs av en speciell Beskrivning av villkoret. 
 
 Om det inte finns någon information, [Skicka feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) och ange information om de moduler som orsakade felet och relaterade villkor.
  
|Undantags meddelanden|  
|------------------------|  
|Åtgärden misslyckades.|  
|Ett fel uppstod när åtgärden slutfördes: {0}.|  
  

## <a name="error-0040"></a>Fel 0040  
 Ett undantag inträffar vid anrop av en föråldrad modul.  
  
 Det här felet i Azure Machine Learning skapas när du anropar en föråldrad modul.  
  
**Lösning:** Ersätt den inaktuella modulen med en som stöds. I modulen för utdata i modulen finns information om vilken modul som ska användas i stället.  
  
|Undantags meddelanden|  
|------------------------|  
|Åtkomst till föråldrad modul.|  
|Modulen "{0}" är föråldrad. Använd modulen "{1}" i stället.|  
 

## <a name="error-0041"></a>Fel 0041  
 Ett undantag inträffar vid anrop av en föråldrad modul.  
  
 Det här felet i Azure Machine Learning skapas när du anropar en föråldrad modul.  
  
**Lösning:** Ersätt den inaktuella modulen med en uppsättning som stöds. Den här informationen ska vara synlig i utdata-loggen för modulen.  
  
|Undantags meddelanden|  
|------------------------|  
|Åtkomst till föråldrad modul.|  
|Modulen "{0}" är föråldrad. Använd modulerna "{1}" för de begärda funktionerna.|  
 

## <a name="error-0042"></a>Fel 0042  
 Ett undantag inträffar när det inte går att konvertera kolumnen till en annan typ.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att konvertera kolumnen till den angivna typen.  Du får det här felet om en modul kräver en viss datatyp, till exempel DateTime, text, flytt ALS nummer eller heltal, men det går inte att konvertera en befintlig kolumn till den typ som krävs.  
 
Du kan till exempel välja en kolumn och försöka konvertera den till en numerisk datatyp som används i en matematik åtgärd och få det här felet om kolumnen innehöll ogiltiga data. 

Ett annat skäl till att du kan få det här felet om du försöker använda en kolumn som innehåller flytt ALS nummer eller många unika värden som en kategoriska kolumn. 
  
**Lösning**

+ Öppna hjälp sidan för den modul som genererade felet och kontrol lera data typs kraven.
+ Granska data typerna för kolumnerna i data uppsättningen för indata.
+ Granska data som har sitt ursprung i så kallade schema mindre data källor.
+ Kontrol lera data uppsättningen för saknade värden eller specialtecken som kan blockera konverteringen till önskad datatyp. 
    + Numeriska data typer bör vara konsekventa: Sök efter flytt ALS nummer i en kolumn med heltal.
    + Sök efter text strängar eller NA-värden i en tal kolumn. 
    + Booleska värden kan konverteras till en lämplig representation beroende på den data typ som krävs.
    + Undersök text kolumner för icke-Unicode-tecken, TABB-tecken eller kontroll tecken
    + Datetime-data bör vara konsekventa för att undvika modellerings fel, men rensning kan vara komplicerat på grund av många format. Överväg att använda <!--the [Execute R Script](execute-r-script.md) or -->Utför rensning genom att [köra Python-skript](execute-python-script.md) moduler.  
+ Om det behövs ändrar du värdena i indata-datauppsättningen så att kolumnen kan konverteras korrekt. Ändringen kan omfatta diskretisering, trunkering eller avrundning, Eli minering av avvikande eller Imputation av värden som saknas. I följande artiklar finns några vanliga data omvandlings scenarier i Machine Learning:
    + [Rensa saknade data](clean-missing-data.md)
    + [Normalisera data](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Är du klar med upplösning eller ej tillämpligt på ditt fall? Du är välkommen att skicka feedback om den här artikeln och ange information om scenariot, inklusive modulen och data typen för kolumnen. Vi kommer att använda den här informationen för att tillhandahålla mer detaljerade fel söknings steg i framtiden.  
  
|Undantags meddelanden|  
|------------------------|  
|Tillåts inte konvertering.|  
|Det gick inte att konvertera kolumnen av typen {0} till kolumnen av typen {1}.|  
|Det gick inte att konvertera kolumnen "{2}" av typen {0} till kolumnen av typen {1}.|  
|Det gick inte att konvertera kolumnen "{2}" av typen {0} till kolumnen "{3}" av typen {1}.|  
  

## <a name="error-0043"></a>Fel 0043  
 Undantag inträffar när element typen inte uttryckligen implementerar är lika med.  
  
 Det här felet i Azure Machine Learning används inte och kommer att vara inaktuellt.  
  
**Lösning:** Alternativet.  
  
|Undantags meddelanden|  
|------------------------|  
|Det finns ingen tillgänglig explicit metod som är lika med.|  
|Det går inte att jämföra värden för kolumnen \\"{0}\\" av typen {1}. Det finns ingen tillgänglig explicit metod som är lika med.|  


## <a name="error-0044"></a>Fel 0044  
 Ett undantag inträffar när det inte går att härleda element typen för kolumnen från befintliga värden.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att härleda typen av en kolumn eller kolumner i en data uppsättning. Detta inträffar vanligt vis när två eller flera data uppsättningar sammanfogas med olika element typer. Om Azure Machine Learning inte kan fastställa en gemensam typ som kan representera alla värden i en kolumn eller kolumner utan att information går förlorad, genereras det här felet.  
  
**Lösning:** Se till att alla värden i en kolumn i båda data uppsättningarna kombineras antingen av samma typ (numeriska, booleska, kategoriska, sträng, datum osv.) eller kan tvingas till samma typ.  
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att härleda element typen för kolumnen.|  
|Det går inte att härleda element typen för kolumnen{0}--alla element är null-referenser.|  
|Det går inte att härleda element typen för kolumnen{0}för data mängden{1}--alla element är null-referenser.|  
  

## <a name="error-0045"></a>Fel 0045  
 Ett undantag inträffar när det inte går att skapa en kolumn på grund av blandade element typer i källan.  
  
 Det här felet i Azure Machine Learning skapas när element typerna för två data uppsättningar som kombineras är olika.  
  
**Lösning:** Se till att alla värden i en specifik kolumn i båda data uppsättningarna kombineras av samma typ (numeriska, booleska, kategoriska, sträng, datum osv.).  
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att skapa en kolumn med blandade element typer.|  
|Det går inte att skapa en kolumn med ID "{0}" för blandade element typer: \ n\tType av data [{1}{0}] är {2}\n\tType data [{3}, {0}] {4}.|  
  

## <a name="error-0046"></a>Fel 0046  
 Undantag inträffar när det inte går att skapa katalogen på den angivna sökvägen.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att skapa en katalog på den angivna sökvägen. Du får det här felet om någon del av sökvägen till utdata-katalogen för en Hive-fråga är felaktig eller inte går att komma åt.  
  
**Lösning:** Gå tillbaka i modulen och kontrol lera att katalog Sök vägen är korrekt formaterad och att den är tillgänglig med de aktuella autentiseringsuppgifterna.  
  
|Undantags meddelanden|  
|------------------------|  
|Ange en giltig utmatnings katalog.|  
|Katalog: det går inte att skapa {0}. Ange en giltig sökväg.|  
  

## <a name="error-0047"></a>Fel 0047  
 Undantag uppstår om antalet funktions kolumner i några av de data uppsättningar som skickats till modulen är för litet.  
  
 Det här felet i Azure Machine Learning uppstår om data uppsättningen för indata till träning inte innehåller det minsta antalet kolumner som krävs av algoritmen. Vanligt vis är antingen data uppsättningen tom eller innehåller bara utbildnings kolumner.  
  
**Lösning:** Gå tillbaka till data uppsättningen för indata för att se till att en eller flera kolumner skiljer sig från kolumnen etikett.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet funktions kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet.|  
|Antalet funktions kolumner i indata-datauppsättningen är mindre än det tillåtna minimivärdet för {0} kolumn (er).|  
|Antalet funktions kolumner i indata-datauppsättningen{0}är mindre än det tillåtna minimivärdet för {1} kolumn (er).|  
  

## <a name="error-0048"></a>Fel 0048  
 Undantag uppstår i fallet när det inte går att öppna en fil.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att öppna en fil för läsning eller skrivning. Du kan få det här felet av följande anledningar:  
  
-   Behållaren eller filen (BLOB) finns inte  
  
-   Åtkomst nivån för filen eller behållaren tillåter inte åtkomst till filen  
  
-   Filen är för stor för att kunna läsas eller fel format  
  
**Lösning:** Gå tillbaka till modulen och filen som du försöker läsa.  
  
 Kontrol lera att namnen på behållaren och filen är korrekta.  
  
 Använd den klassiska Azure-portalen eller ett Azure Storage-verktyg för att kontrol lera att du har behörighet att komma åt filen.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att öppna en fil.|  
|Ett fel uppstod när filen öppnades: {0}.|  


## <a name="error-0049"></a>Fel 0049  
 Undantag uppstår i fallet när det inte går att parsa en fil.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att parsa en fil. Du får det här felet om fil formatet som valts i modulen [Importera data](import-data.md) inte matchar det faktiska formatet för filen, eller om filen innehåller ett okänt.  
  
**Lösning:** Gå tillbaka till modulen och korrigera fil formats valet om den inte matchar fil formatet. Om möjligt bör du kontrol lera att filen inte innehåller några ogiltiga tecken.  
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att parsa en fil.|  
|Fel vid parsning av filen: {0}.|  
  

## <a name="error-0050"></a>Fel 0050  
 Undantag inträffar i fallet när indata-och utdatafiler är desamma.  
  
**Lösning:** Det här felet i Azure Machine Learning används inte och kommer att vara inaktuellt.  
  
|Undantags meddelanden|  
|------------------------|  
|De angivna filerna för indata och utdata får inte vara samma.|


## <a name="error-0051"></a>Fel 0051  
 Undantag inträffar i fallet när flera utdatafiler är desamma.  
  
**Lösning:** Det här felet i Azure Machine Learning används inte och kommer att vara inaktuellt.  
  
|Undantags meddelanden|  
|------------------------|  
|De angivna filerna för utdata får inte vara samma.|


## <a name="error-0052"></a>Fel 0052  
 Undantag uppstår om nyckeln för Azure Storage-kontot har angetts felaktigt.  
  
 Det här felet uppstår i Azure Machine Learning om nyckeln som används för att komma åt Azure Storage-kontot är felaktig. Du kan till exempel se det här felet om Azure Storage-nyckeln trunkerades när den kopierades och klistrades in, eller om fel nyckel användes.  
  
 Mer information om hur du hämtar nyckeln för ett Azure Storage-konto finns i [Visa, kopiera och återskapa lagrings åtkomst nycklar](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Lösning:** Gå tillbaka modulen och kontrol lera att Azure Storage-nyckeln är korrekt för kontot. Kopiera nyckeln igen från den klassiska Azure-portalen om det behövs.  
  
|Undantags meddelanden|  
|------------------------|  
|Azure Storage-kontots nyckel är felaktig.|  
  

## <a name="error-0053"></a>Fel 0053  
 Undantag inträffar i fallet när det inte finns några användar funktioner eller objekt för matchbox-rekommendationer.  
  
 Det här felet i Azure Machine Learning skapas när det inte går att hitta en funktions vektor.  
  
**Lösning:** Se till att det finns en funktions vektor i indata-datauppsättningen.  
  
|Undantags meddelanden|  
|------------------------|  
|Användar funktioner eller/och objekt krävs, men har inte angetts.|  

## <a name="error-0054"></a>Fel 0054  
 Undantaget inträffar om det finns för få distinkta värden i kolumnen för att slutföra åtgärden.  
  
**Lösning:** Det här felet i Azure Machine Learning används inte och kommer att vara inaktuellt.  
  
|Undantags meddelanden|  
|------------------------|  
|Data har för få distinkta värden i den angivna kolumnen för att slutföra åtgärden.|  
|Data har för få distinkta värden i den angivna kolumnen för att slutföra åtgärden. Det obligatoriska minimivärdet är {0} element.|  
|Data har för få distinkta värden i kolumnen{1}för att slutföra åtgärden. Det obligatoriska minimivärdet är {0} element.|  
  

## <a name="error-0055"></a>Fel 0055  
 Ett undantag inträffar vid anrop av en föråldrad modul.  Det här felet i Azure Machine Learning visas om du försöker anropa en modul som är föråldrad.
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Åtkomst till föråldrad modul.|  
|Modulen "{0}" är föråldrad.|  

## <a name="error-0056"></a>Fel 0056  
 Undantag uppstår om de kolumner som du har valt för en åtgärd strider mot kraven.  
  
 Det här felet uppstår Azure Machine Learning när du väljer kolumner för en åtgärd som kräver att kolumnen är av en viss datatyp. 
 
 Det här felet kan också inträffa om kolumnen har rätt datatyp, men modulen som du använder kräver att kolumnen också är markerad som en funktions-, etikett-eller kategoriska-kolumn.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Lösning**
  
1.  Granska data typen för de kolumner som för närvarande är markerade. 

2. Kontrol lera om de markerade kolumnerna är kategoriska, Label eller Feature columns.  
  
3.  Granska hjälp avsnittet för modulen där du gjorde kolumn urvalet för att avgöra om det finns särskilda krav för data typen eller kolumn användningen.  
  
3.  Använd [Redigera metadata](edit-metadata.md) om du vill ändra kolumn typen för den här åtgärdens varaktighet. Se till att ändra kolumn typen tillbaka till det ursprungliga värdet med hjälp av en annan instans av [Redigera metadata](edit-metadata.md), om du behöver den för efterföljande åtgärder.  
  
|Undantags meddelanden|  
|------------------------|  
|En eller flera valda kolumner fanns inte i en tillåten kategori.|  
|Kolumnen med namnet{0}finns inte i en tillåten kategori.|  
  

## <a name="error-0057"></a>Fel 0057  
 Ett undantag uppstår vid försök att skapa en fil eller BLOB som redan finns.  
  
 Detta undantag uppstår när du använder modulen [Exportera data](export-data.md) eller annan modul för att spara resultaten av en pipeline i Azure Machine Learning till Azure Blob Storage, men du försöker skapa en fil eller BLOB som redan finns.   
  
**Lösning**
 
 Du får bara det här felet om du tidigare har ställt in egenskaps **skrivnings läget för Azure Blob Storage** på **fel**. Enligt design genererar den här modulen ett fel om du försöker skriva en data uppsättning till en blob som redan finns.
 
 - Öppna modulens egenskaper och ändra egenskapen **Azure Blob Storage Skriv läge** till **Skriv över**.
 - Alternativt kan du ange namnet på en annan målfil eller fil och se till att du anger en blob som inte redan finns.  
  
|Undantags meddelanden|  
|------------------------|  
|Filen eller blobben finns redan.|  
|Filen eller blobben "{0}" finns redan.|  
  

## <a name="error-0058"></a>Fel 0058  
 Det här felet uppstår i Azure Machine Learning om data uppsättningen inte innehåller den förväntade etikett kolumnen.  
  
 Detta undantag kan också inträffa när den angivna etikett kolumnen inte matchar data eller datatyp som förväntas av en elev eller har fel värden. Detta undantag skapas till exempel när du använder en kolumn med Real värdes etiketter när du tränar en binär klassificerare.  
  
**Lösning:** Upplösningen beror på den eller de utbildare som du använder och data typerna för kolumnerna i data uppsättningen. Kontrol lera först kraven för Machine Learning-algoritmen eller modulen utbildning.  
  
 Gå tillbaka till indata-datauppsättningen. Kontrol lera att kolumnen du förväntar dig att behandlas som etikett har rätt datatyp för den modell som du skapar.  
  
 Kontrol lera indata för saknade värden och eliminera eller ersätt dem om det behövs.  
  
 Om det behövs lägger du till modulen [Redigera metadata](edit-metadata.md) och kontrollerar att kolumnen etikett är markerad som en etikett.  
  
|Undantags meddelanden|  
|------------------------|  
|Etikett kolumnen är inte som förväntat|  
|Etikett kolumnen är inte som förväntat i{0}.|  
|Etikett kolumnen "{0}" förväntas inte i "{1}".|  
  

## <a name="error-0059"></a>Fel 0059  
 Undantag uppstår om ett kolumn index som har angetts i en kolumn väljare inte kan parsas.  
  
 Det här felet uppstår i Azure Machine Learning om ett kolumn index som anges när du använder kolumn väljaren inte kan parsas.  Det här felet visas när kolumn indexet har ett ogiltigt format som inte går att parsa.  
  
**Lösning:** Ändra kolumn indexet så att det använder ett giltigt index värde.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att parsa ett eller flera angivna kolumn index eller index intervall.|  
|Det gick inte att parsa kolumn indexet eller området{0}.|  
  

## <a name="error-0060"></a>Fel 0060  
 Undantag inträffar när ett kolumn intervall utanför intervallet anges i en kolumn väljare.  
  
 Det här felet uppstår i Azure Machine Learning när ett kolumn intervall utanför intervallet anges i kolumn väljaren. Det här felet visas om kolumn intervallet i kolumn väljaren inte motsvarar kolumnerna i data uppsättningen.  
  
**Lösning:** Ändra kolumn intervallet i kolumn väljaren så att det motsvarar kolumnerna i data uppsättningen.  
  
|Undantags meddelanden|  
|------------------------|  
|Det angivna kolumn index intervallet är ogiltigt eller utanför intervallet.|  
|Kolumn intervallet "{0}" är ogiltigt eller utanför intervallet.|  
  

## <a name="error-0061"></a>Fel 0061  
 Ett undantag uppstår vid försök att lägga till en rad i en DataTable som har ett annat antal kolumner än tabellen.  
  
 Det här felet uppstår i Azure Machine Learning när du försöker lägga till en rad i en data uppsättning som har ett annat antal kolumner än data uppsättningen.  Du får det här felet om den rad som läggs till i data uppsättningen har ett annat antal kolumner från indata-datauppsättningen.  Det går inte att lägga till raden i data uppsättningen om antalet kolumner är olika.  
  
**Lösning:** Ändra indata-datauppsättningen till att ha samma antal kolumner som den tillagda raden, eller ändra raden som lagts till i har samma antal kolumner som data uppsättningen.  
  
|Undantags meddelanden|  
|------------------------|  
|Alla tabeller måste ha samma antal kolumner.|  
  

## <a name="error-0062"></a>Fel 0062  
 Ett undantag uppstår vid försök att jämföra två modeller med olika typer av information.  
  
 Det här felet i Azure Machine Learning skapas när utvärderings måtten för två olika Poäng data uppsättningar inte kan jämföras. I det här fallet är det inte möjligt att jämföra effektiviteten hos de modeller som används för att producera de två poäng data uppsättningarna.  
  
**Lösning:** Kontrol lera att resultat resultaten produceras av samma typ av Machine Learning-modell (binära klassificering, regression, klassificering av flera klasser, rekommendation, klustring, avvikelse identifiering osv.) Alla modeller som du jämför måste ha samma elev typ.  
  
|Undantags meddelanden|  
|------------------------|  
|Alla modeller måste ha samma elev typ.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning designer, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the pipeline to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the pipeline.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to pipelines in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Undantags meddelanden|  
|------------------------|  
|Fel vid utvärdering av R-skript.|  
|Följande fel uppstod under utvärderingen av R-skriptet:----------början av fel meddelandet från R----------{0}-----------slutet av fel meddelandet från R-----------|  
|Följande fel inträffade under utvärderingen av R-skriptet{1}:----------början av fel meddelandet från R----------{0}-----------slutet av fel meddelandet från R-----------|  
  


## <a name="error-0064"></a>Fel 0064  
 Undantag uppstår om namnet eller lagrings nyckeln för Azure Storage-kontot har angetts felaktigt.  
  
 Det här felet uppstår i Azure Machine Learning om namnet eller lagrings nyckeln för Azure Storage-kontot har angetts felaktigt. Du får det här felet om du anger ett felaktigt konto namn eller lösen ord för lagrings kontot. Detta kan inträffa om du anger konto namnet eller lösen ordet manuellt. Det kan också inträffa om kontot har tagits bort.  
  
**Lösning:** Kontrol lera att konto namnet och lösen ordet har angetts korrekt och att kontot finns.  
  
|Undantags meddelanden|  
|------------------------|  
|Namnet på Azure Storage-kontot eller lagrings nyckeln är felaktigt.|  
|Namnet på Azure Storage-kontot "{0}" eller lagrings nyckeln för konto namnet är felaktigt.|  
  

## <a name="error-0065"></a>Fel 0065  
 Ett undantag uppstår om ett Azure-Blob-namn har angetts felaktigt.  
  
 Det här felet uppstår i Azure Machine Learning om Azure Blob-namnet har angetts felaktigt.  Du får ett fel meddelande om:  
  
-   Det går inte att hitta blobben i den angivna behållaren.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Endast behållaren angavs som källa i en begäran om att [Importera data](import-data.md) när formatet var Excel eller CSV med kodning; sammanfogning av innehållet i alla blobar i en behållare tillåts inte med dessa format.  
  
-   En SAS-URI innehåller inte namnet på en giltig blob.  
  
**Lösning:** När du går tillbaka till modulen utlöses undantaget. Kontrol lera att den angivna blobben finns i behållaren i lagrings kontot och att du kan se blobben i den behörigheten. Kontrol lera att indatafilen har formatet **ContainerName/filename** om du har Excel eller CSV med kodnings format. Kontrol lera att en SAS-URI innehåller namnet på en giltig blob.  
  
|Undantags meddelanden|  
|------------------------|  
|Azure Storage-blobben är felaktig.|  
|Namnet på Azure Storage-blobben "{0}" är felaktigt|  
  

## <a name="error-0066"></a>Fel 0066  
 Ett undantag uppstår om en resurs inte kunde överföras till en Azure-blob.  
  
 Det här felet uppstår i Azure Machine Learning om en resurs inte kunde överföras till en Azure-blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Båda sparas i samma Azure Storage-konto som kontot som innehåller indatafilen.  
  
**Lösning:** Gå tillbaka i modulen. Kontrol lera att namnet på Azure-kontot, lagrings nyckeln och behållaren är korrekta och att kontot har behörighet att skriva till behållaren.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att överföra resursen till Azure Storage.|  
|Filen "{0}" kunde inte överföras till Azure Storage som {1}.|  
  

## <a name="error-0067"></a>Fel 0067  
 Undantag uppstår om en data uppsättning har ett annat antal kolumner än förväntat.  
  
 Det här felet uppstår i Azure Machine Learning om en data uppsättning har ett annat antal kolumner än förväntat.  Du får det här felet när antalet kolumner i data uppsättningen skiljer sig från antalet kolumner som modulen förväntar sig under körningen.  
  
**Lösning:** Ändra indata-datauppsättningen eller parametrarna.  
  
|Undantags meddelanden|  
|------------------------|  
|Ett oväntat antal kolumner i DataTable.|  
|{0}kolumner förväntades men{1}kolumner hittades i stället.|  
  

## <a name="error-0068"></a>Fel 0068  
 Undantag uppstår om det angivna Hive-skriptet inte är korrekt.  
  
 Det här felet uppstår i Azure Machine Learning om det finns syntaxfel i ett Hive-skript eller om Hive-tolken påträffar ett fel vid körning av frågan eller skriptet.  
  
**Lösning**

Fel meddelandet från Hive rapporteras normalt tillbaka i fel loggen så att du kan vidta åtgärder baserat på det specifika felet. 

+ Öppna modulen och undersök frågan efter misstag.  
+ Kontrol lera att frågan fungerar korrekt utanför Azure Machine Learning genom att logga in på Hive-konsolen för ditt Hadoop-kluster och köra frågan.  
+ Försök att placera kommentarer i Hive-skriptet på en separat rad i stället för att blanda körbara program satser och kommentarer på en enda rad.  

### <a name="resources"></a>Resurser

Se följande artiklar för hjälp med Hive-frågor för Machine Learning:

+ [Skapa Hive-tabeller och läsa in data från Azure Blob Storage](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Utforska data i tabeller med Hive-frågor](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive för SQL-användare lathund Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Undantags meddelanden|  
|------------------------|  
|Hive-skriptet är felaktigt.|  
|Hive-skriptet {0} är felaktigt.|  
  

## <a name="error-0069"></a>Fel 0069  
 Undantag uppstår om det angivna SQL-skriptet inte är korrekt.  
  
 Det här felet uppstår i Azure Machine Learning om det angivna SQL-skriptet har syntaxfel, eller om kolumnerna eller tabellen som anges i skriptet inte är giltiga. 
 
 Du får det här felet om SQL-motorn påträffar ett fel när frågan eller skriptet körs. SQL-felmeddelande rapporteras normalt igen i fel loggen så att du kan vidta åtgärder baserat på det specifika felet.  
  
**Lösning:** Gå tillbaka i modulen och granska SQL-frågan för misstag.  
  
 Kontrol lera att frågan fungerar korrekt utanför Azure ML genom att logga in på databas servern direkt och köra frågan.  
  
 Om det finns ett SQL-genererat meddelande som rapporter ATS av ett modul undantag, vidta åtgärder baserat på det rapporterade felet. Fel meddelandena innehåller till exempel ibland speciell vägledning om det troliga felet:
+ *Ingen sådan kolumn eller saknad databas*, vilket indikerar att du har angett ett kolumn namn som fel. Om du är säker på att kolumn namnet är korrekt kan du prova att använda hakparenteser eller citat tecken för att omsluta kolumn-ID: n.
+ *SQL Logic-fel nära \<SQL-nyckelord\>* , vilket tyder på att du kan ha ett syntaxfel före det angivna nyckelordet

  
|Undantags meddelanden|  
|------------------------|  
|SQL-skriptet är felaktigt.|  
|SQL-frågan "{0}" är felaktig.|  
|SQL-frågan "{0}" är felaktig: {1}|  
  

## <a name="error-0070"></a>Fel 0070  
 Ett undantag uppstår vid försök att komma åt en Azure-tabell som inte finns.  
  
 Det här felet uppstår Azure Machine Learning när du försöker komma åt en Azure-tabell som inte finns. Du får det här felet om du anger en tabell i Azure Storage, som inte finns när du läser från eller skriver till Azure Table Storage. Detta kan inträffa om du skriver in namnet på den önskade tabellen eller om du har ett matchnings fel mellan mål namnet och lagrings typen. Du kan till exempel läsa från en tabell men ange namnet på en BLOB i stället.  
  
**Lösning:** Gå tillbaka i modulen för att kontrol lera att namnet på tabellen är korrekt.  
  
|Undantags meddelanden|  
|------------------------|  
|Azure-tabellen finns inte.|  
|Azure-tabellen{0}finns inte.|  
  
## <a name="error-0071"></a>Fel 0071  
 Undantag uppstår om de angivna autentiseringsuppgifterna är felaktiga.  
  
 Det här felet uppstår i Azure Machine Learning om de angivna autentiseringsuppgifterna är felaktiga.  
  
 Du kan också få det här felet om modulen inte kan ansluta till ett HDInsight-kluster.  
  
**Lösning:** Granska indata till modulen och kontrol lera konto namnet och lösen ordet.  
  
 Sök efter följande problem som kan orsaka ett fel:  
  
-   Data uppsättningens schema matchar inte schemat för mål-DataTable.  
  
-   Kolumn namn saknas eller är felstavade  
  
-   Du skriver till en tabell som har kolumn namn med ogiltiga tecken. Vanligt vis kan du sätta sådana kolumn namn inom hakparenteser, men om det inte fungerar kan du redigera kolumn namn så att endast bokstäver och under streck (_) används  
  
-   Strängar som du försöker skriva innehåller enkla citat tecken  
  
 Om du försöker ansluta till ett HDInsight-kluster kontrollerar du att mål klustret är tillgängligt med de angivna autentiseringsuppgifterna.  
  
|Undantags meddelanden|  
|------------------------|  
|Felaktiga autentiseringsuppgifter har skickats.|  
|Felaktigt användar namn{0}eller lösen ord har skickats|  
  

## <a name="error-0072"></a>Fel 0072  
 Undantag inträffar om tids gränsen för anslutningen uppnåddes.  
  
 Det här felet uppstår i Azure Machine Learning när tids gränsen för anslutningen uppnåddes. Du får det här felet om det finns problem med anslutningen till data källan eller målet, t. ex. långsam Internet anslutning, eller om data uppsättningen är stor och/eller om SQL-frågan för att läsa i data utför komplicerad bearbetning.  
  
**Lösning:** Ta reda på om det finns problem med långsamma anslutningar till Azure Storage eller Internet.  
  
|Undantags meddelanden|  
|------------------------|  
|Tids gränsen för anslutningen har inträffat.|  
  

## <a name="error-0073"></a>Fel 0073  
 Undantag uppstår om ett fel inträffar när en kolumn konverteras till en annan typ.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att konvertera kolumnen till en annan typ.  Det här felet visas om en modul kräver en viss typ och det inte går att konvertera kolumnen till den nya typen.  
  
**Lösning:** Ändra indata-datauppsättningen så att kolumnen kan konverteras baserat på det inre undantaget.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att konvertera kolumnen.|  
|Det gick inte att konvertera kolumnen till {0}.|  
  

## <a name="error-0074"></a>Fel 0074  
 Ett undantag inträffar när [Redigera metadata](edit-metadata.md) försöker konvertera en null-optimerad kolumn till kategoriska.  
  
 Det här felet uppstår i Azure Machine Learning när [Redigera metadata](edit-metadata.md) försöker konvertera en null-optimerad kolumn till kategoriska.  Du får det här felet när du försöker konvertera null-optimerade kolumner till kategoriska med alternativet **gör kategoriska** .  Azure Machine Learning stöder inte sparse kategoriska-matriser, så modulen kommer inte att fungera.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att konvertera null-optimerade kolumner till kategoriska.|  
  

## <a name="error-0075"></a>Fel 0075  
Undantag inträffar när en ogiltig diskretisering-funktion används vid quantizing av en data uppsättning.  
  
Det här felet uppstår i Azure Machine Learning när du försöker att använda bin-data med en metod som inte stöds, eller när parameter kombinationerna är ogiltiga.  
  
**Lösning**

Fel hantering för den här händelsen introducerades i en tidigare version av Azure Machine Learning som tillåter mer anpassning av diskretisering-metoder. För närvarande är alla diskretisering-metoder baserade på ett val från en nedrullningsbar listruta, så tekniskt sett bör det inte längre vara möjligt att få det här felet.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Undantags meddelanden|  
|------------------------|  
|Ogiltig diskretisering-funktion används.|  
  

## <a name="error-0077"></a>Fel 0077  
 Undantag inträffar när ett okänt skrivnings läge för BLOB-filen har överförts.  
  
 Det här felet uppstår i Azure Machine Learning om ett ogiltigt argument skickas i specifikationerna för en BLOB-fil eller källa.  
  
**Lösning:** I nästan alla moduler som importerar eller exporterar data till och från Azure Blob Storage, tilldelas parameter värden som styr skrivnings läget med hjälp av en nedrullningsbar listruta. Därför är det inte möjligt att skicka ett ogiltigt värde och det här felet bör inte visas. Det här felet kommer att vara inaktuellt i en senare version.  
  
|Undantags meddelanden|  
|------------------------|  
|BLOB-skrivnings läget stöds inte.|  
|BLOB-skrivnings läge som inte stöds: {0}.|  
  

## <a name="error-0078"></a>Fel 0078  
 Undantag inträffar när HTTP-alternativet för [Importera data](import-data.md) får en 3xx-status kod som anger omdirigering.  
  
 Det här felet uppstår i Azure Machine Learning när HTTP-alternativet för [Importera data](import-data.md) får en 3xx (301, 302, 304 osv.) som anger omdirigering. Du får det här felet om du försöker ansluta till en HTTP-källa som omdirigerar webbläsaren till en annan sida. Av säkerhets skäl är det inte tillåtet att omdirigera webbplatser som data källor för Azure Machine Learning.  
  
**Lösning:** Om webbplatsen är en betrodd webbplats anger du den omdirigerade URL: en direkt.  
  
|Undantags meddelanden|  
|------------------------|  
|Http-omdirigering tillåts inte|  
  

## <a name="error-0079"></a>Fel 0079  
 Undantag uppstår om namn på Azure Storage container anges felaktigt.  
  
 Felet i Azure Machine Learning uppstår om namnet på Azure Storage-behållaren är felaktigt angivet. Du får det här felet om du inte har angett både behållaren och blob-namnet (fil) med hjälp **av sökvägen till bloben som börjar med container** -alternativet när du skriver till Azure Blob Storage.  
  
**Lösning:** Gå tillbaka till modulen [Exportera data](export-data.md) och kontrol lera att den angivna sökvägen till blobben innehåller både behållaren och fil namnet i formatet **container/filename**.  
  
|Undantags meddelanden|  
|------------------------|  
|Namnet på Azure Storage-behållaren är felaktigt.|  
|Namnet på Azure Storage-behållaren "{0}" är felaktigt. ett behållar namn för format containern/blobben förväntades.|  
  

## <a name="error-0080"></a>Fel 0080  
 Undantag inträffar när kolumnen med alla värden som saknas inte tillåts av modulen.  
  
 Det här felet i Azure Machine Learning skapas när en eller flera av kolumnerna som används av modulen innehåller alla värden som saknas. Om en modul exempelvis beräknar sammanställd statistik för varje kolumn, kan den inte användas i en kolumn som inte innehåller några data. I sådana fall stoppas modul körningen med detta undantag.  
  
**Lösning:** Gå tillbaka till indata-datauppsättningen och ta bort alla kolumner som innehåller alla saknade värden.  
  
|Undantags meddelanden|  
|------------------------|  
|Kolumner med värden som saknas är inte tillåtna.|  
|Kolumn {0} saknar värden.|  
  

## <a name="error-0081"></a>Fel 0081  
 Undantag uppstår i PCA-modul om antalet dimensioner som ska minskas till är lika med antalet funktions kolumner i indata-datauppsättningen som innehåller minst en kolumn för sparse-funktioner.  
  
 Det här felet i Azure Machine Learning skapas om följande villkor uppfylls: (a) data uppsättningen för indata innehåller minst en null-optimerad kolumn och (b) det slutgiltiga antalet dimensioner som krävs är samma som antalet ingångs dimensioner.  
  
**Lösning:** Överväg att minska antalet dimensioner i utdatan till färre än antalet dimensioner i indata. Detta är vanligt i program av PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Undantags meddelanden|  
|------------------------|  
|För data uppsättning som innehåller null-optimerade funktions kolumner ska de dimensioner som ska minskas vara mindre än antalet funktions kolumner.|  
 

## <a name="error-0082"></a>Fel 0082  
 Ett undantag inträffar när en modell inte kan avserialiseras.  
  
 Det här felet uppstår i Azure Machine Learning när en sparad Machine Learning-modell eller-transformering inte kan läsas in med en nyare version av Azure Machine Learning runtime som ett resultat av en brytande ändring.  
  
**Lösning:** Den utbildnings pipelinen som skapade modellen eller omvandlingen måste köras igen och modellen eller transformeringen måste sparas på nytt.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att deserialisera modellen eftersom den förmodligen serialiseras med ett äldre format för serialisering. Omträna och spara om modellen.|  
  

## <a name="error-0083"></a>Fel 0083  
 Ett undantag uppstår om data uppsättningen som används för träning inte kan användas för en konkret typ av elev.  
  
 Det här felet i Azure Machine Learning skapas när data uppsättningen är inkompatibel med den elev som tränas. Data uppsättningen kan till exempel innehålla minst ett värde som saknas i varje rad, och därför skulle hela data uppsättningen hoppas över under träningen. I andra fall förväntas inte några Machine Learning-algoritmer, till exempel avvikelse identifiering, etiketter visas och kan utlösa detta undantag om det finns etiketter i data uppsättningen.  
  
**Lösning:** Läs dokumentationen för den handare som används för att kontrol lera kraven för data uppsättningen för indata. Granska kolumnerna för att se att alla obligatoriska kolumner finns.  
  
|Undantags meddelanden|  
|------------------------|  
|Data uppsättningen som används för träning är ogiltig.|  
|{0} innehåller ogiltiga data för träning.|  
|{0} innehåller ogiltiga data för träning. Elev typ: {1}.|  
  

## <a name="error-0084"></a>Fel 0084  
 Undantag inträffar när resultat som skapats från ett R-skript utvärderas. Detta stöds för närvarande inte.  
  
 Felet i Azure Machine Learning uppstår om du försöker använda en av modulerna för att utvärdera en modell med utdata från ett R-skript som innehåller resultat.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Det finns för närvarande inte stöd för att utvärdera poängen som skapats av R.|  
  

## <a name="error-0085"></a>Fel 0085  
 Undantag inträffar när skript utvärderingen Miss lyckas med ett fel.  
  
 Det här felet uppstår Azure Machine Learning när du kör anpassade skript som innehåller syntaxfel.  
  
**Lösning:** Granska koden i ett externt redigerings program och Sök efter fel.  
  
|Undantags meddelanden|  
|------------------------|  
|Fel vid utvärdering av skript.|  
|Följande fel uppstod under skript utvärderingen. Visa utgående logg för mer information:----------start av fel meddelande från {0} tolken----------{1}----------slutet av fel meddelandet från {0} tolk----------|  
  

## <a name="error-0086"></a>Fel 0086  
 Ett undantag inträffar när en räknings omvandling är ogiltig.  
  
 Det här felet uppstår i Azure Machine Learning när du väljer en omvandling som baseras på en Count-tabell, men den valda transformeringen är inkompatibel med aktuella data eller med den nya Count-tabellen.  
  
**Lösning:** Modulen stöder sparande av de antal och regler som utgör omvandlingen i två olika format. Om du sammanfogar Count-tabeller kontrollerar du att båda tabellerna som du vill sammanfoga använder samma format.  
  
I allmänhet kan en räknad transformering endast tillämpas på data uppsättningar som har samma schema som den data uppsättning som transformationen ursprungligen skapades på.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Undantags meddelanden|  
|------------------------|  
|Ogiltig uppräknings transformering har angetts.|  
|Räknings omvandlingen vid Indataporten{0}är ogiltig.|  
|Det går inte att sammanfoga räknings omvandlingen vid Indataporten{0}med Indataporten för Indataporten{1}. Kontrol lera vilka metadata som används för att räkna antalet matchningar.|  
  

## <a name="error-0087"></a>Fel 0087  
 Ett undantag inträffar när en ogiltig Count-tabell typ anges för moduler för inlärning med antal.  
  
 Det här felet uppstår Azure Machine Learning när du försöker importera en befintlig Count-tabell, men tabellen är inkompatibel med aktuella data eller med den nya Count-tabellen.  
  
**Lösning:** Det finns olika format för att spara de antal och regler som utgör omvandlingen. Om du sammanfogar Count-tabeller kontrollerar du att båda använder samma format.  
  
 I allmänhet kan en räknad transformering endast tillämpas på data uppsättningar som har samma schema som den data uppsättning som transformationen ursprungligen skapades på.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Fel 0088  
 Ett undantag inträffar när en ogiltig inventerings typ har angetts för inlärning med Count-moduler.  
  
 Det här felet uppstår Azure Machine Learning när du försöker använda en annan inventerings metod än vad som stöds för Count-baserade funktionalisering.  
  
**Lösning:** I allmänhet väljs inventerings metoder från en listruta, så du bör inte se det här felet.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Undantags meddelanden|  
|------------------------|  
|Ogiltig inventerings typ har angetts.|  
|Den angivna inventerings typen{0}är inte en giltig beräknings typ.|  
  

## <a name="error-0089"></a>Fel 0089  
 Undantag inträffar när det angivna antalet klasser är mindre än det faktiska antalet klasser i en data mängd som används för inventering.  
  
 Det här felet uppstår i Azure Machine Learning när du skapar en Count-tabell och kolumnen Label innehåller ett annat antal klasser än du angav i modulens parametrar.  
  
**Lösning:** Kontrol lera din data uppsättning och ta reda på exakt hur många distinkta värden (möjliga klasser) som finns i kolumnen etikett. När du skapar Count-tabellen måste du ange minst det här antalet klasser.  
  
 Count-tabellen kan inte automatiskt fastställa antalet tillgängliga klasser.  
  
 När du skapar Count-tabellen kan du inte ange 0 eller ett tal som är mindre än det faktiska antalet klasser i kolumnen etikett.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet klasser är felaktigt. Kontrol lera att antalet klasser som du anger i parameter fönstret är större än eller lika med antalet klasser i kolumnen etikett.|  
|Antalet angivna klasser är{0}, vilket inte är större än ett etikett värde{1}i den data mängd som används för att räkna. Kontrol lera att antalet klasser som du anger i parameter fönstret är större än eller lika med antalet klasser i kolumnen etikett.|  
  

## <a name="error-0090"></a>Fel 0090  
 Undantag inträffar när det inte går att skapa Hive-tabell.  
  
 Det här felet uppstår i Azure Machine Learning när du använder [export data](export-data.md) eller ett annat alternativ för att spara data i ett HDInsight-kluster och det inte går att skapa den angivna Hive-tabellen.  
  
**Lösning:** Kontrol lera namnet på det Azure Storage-konto som är associerat med klustret och kontrol lera att du använder samma konto i egenskaperna för modulen.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att skapa Hive-tabellen. För ett HDInsight-kluster ser du till att namnet på det Azure Storage-konto som är associerat med klustret är detsamma som det som skickas genom parametern modul.|  
|Det gick inte att skapa Hive-tabellen "{0}". För ett HDInsight-kluster ser du till att namnet på det Azure Storage-konto som är associerat med klustret är detsamma som det som skickas genom parametern modul.|  
|Det gick inte att skapa Hive-tabellen "{0}". För ett HDInsight-kluster ser du till att namnet på det Azure Storage-konto som är associerat med klustret är "{1}".|  
 

## <a name="error-0100"></a>Fel 0100  
 Undantag inträffar när ett språk som inte stöds har angetts för en anpassad modul.  
  
 Det här felet uppstår i Azure Machine Learning när en anpassad modul skapas och egenskapen Name för **språk** elementet i en XML-definitions fil för anpassad modul har ett ogiltigt värde. För närvarande är det enda giltiga värdet för den här egenskapen `R`. Till exempel:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Lösning:** Kontrol lera att namn egenskapen för **språk** elementet i XML-konfigurationsfilen för anpassad modul har angetts till `R`. Spara filen, uppdatera zip-paketet för den anpassade modulen och försök att lägga till den anpassade modulen igen.  
  
|Undantags meddelanden|  
|------------------------|  
|Språket för en anpassad modul som inte stöds har angetts|  
  

## <a name="error-0101"></a>Fel 0101  
 Alla port-och parameter-ID: n måste vara unika.  
  
 Det här felet uppstår i Azure Machine Learning när en eller flera portar eller parametrar tilldelas samma ID-värde i en XML-definitions fil för en anpassad modul.  
  
**Lösning:** Kontrol lera att ID-värdena för alla portar och parametrar är unika. Spara XML-filen, uppdatera zip-paketet för den anpassade modulen och försök att lägga till den anpassade modulen igen.  
  
|Undantags meddelanden|  
|------------------------|  
|Alla port-och parameter-ID för en modul måste vara unika|  
|Modulen{0}har dubbla port-/argument-ID: n. Alla port-/argument-ID: n måste vara unika för en modul.|  
  

## <a name="error-0102"></a>Fel 0102  
 Genereras när en ZIP-fil inte kan extraheras.  
  
 Det här felet uppstår i Azure Machine Learning när du importerar ett zippat paket med fil namns tillägget. zip, men paketet är inte en zip-fil, eller så använder filen inte ett zip-format som stöds.  
  
**Lösning:** Kontrol lera att den valda filen är en giltig. zip-fil och att den har komprimerats med hjälp av någon av de algoritmer som stöds.  
  
 Om du får det här felet när du importerar data uppsättningar i komprimerat format måste du kontrol lera att alla filer som finns i filen använder något av de fil format som stöds och att de är i Unicode-format.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Försök att lägga till de önskade filerna i en ny komprimerad zippad mapp och försök att lägga till den anpassade modulen igen.  
  
|Undantags meddelanden|  
|------------------------|  
|Den angivna ZIP-filen har inte rätt format|  


## <a name="error-0103"></a>Fel 0103  
 Utlöstes när en ZIP-fil inte innehåller några XML-filer  
  
 Det här felet uppstår i Azure Machine Learning när zip-paketet för den anpassade modulen inte innehåller några modul definitions-filer (. xml). Dessa filer måste finnas i roten i zip-paketet (till exempel inte i en undermapp.)  
  
**Lösning:** Kontrol lera att en eller flera definitions filer för XML-modul finns i rotmappen i zip-paketet genom att extrahera den till en tillfällig mapp på disk enheten. Alla XML-filer ska vara direkt i den mapp som du extraherade zip-paketet till. Se till att du skapar zip-paketet som du inte väljer en mapp som innehåller XML-filer till zip eftersom detta skapar en underordnad mapp i zip-paketet med samma namn som den mapp som du valde att zippa.  
  
|Undantags meddelanden|  
|------------------------|  
|Den angivna ZIP-filen innehåller inte några modul definitions-filer (XML-filer)|  


## <a name="error-0104"></a>Fel 0104  
 Genereras när en modul definitions fil refererar till ett skript som inte kan hittas  
  
 Det här felet uppstår i Azure Machine Learning när en XML-definitions fil för en anpassad modul refererar till en skript fil i det **språk** element som inte finns i zip-paketet. Sökvägen till skript filen definieras i egenskapen **sourceFile** för **språk** elementet. Sökvägen till käll filen är relativ till roten i zip-paketet (samma plats som XML-filerna för modulen). Om skript filen finns i en undermapp måste den relativa sökvägen till skript filen anges. Om till exempel alla skript har lagrats i en mina **skript** -mapp i zip-paketet måste **språk** elementet lägga till den här sökvägen till egenskapen **sourceFile** enligt nedan. Till exempel:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Lösning:** Kontrol lera att värdet för egenskapen **sourceFile** i **språk** elementet i XML-definitionen för anpassad modul är korrekt och att käll filen finns i rätt relativ sökväg i zip-paketet.  
  
|Undantags meddelanden|  
|------------------------|  
|Den refererade R-skript filen finns inte.|  
|Det gick inte att hitta den refererade R Script-filen{0}. Se till att den relativa sökvägen till filen är korrekt från definitions platsen.|  


## <a name="error-0105"></a>Fel 0105  
 Det här felet visas när en modul definitions fil innehåller en parameter typ som inte stöds  
  
 Det här felet i Azure Machine Learning skapas när du skapar en XML-definition för en anpassad modul och typen av parameter eller argument i definitionen inte matchar en typ som stöds.  
  
**Lösning:** Se till att type-egenskapen för ett **arg** -element i XML-definitions filen för anpassad modul är en typ som stöds.  
  
|Undantags meddelanden|  
|------------------------|  
|Parameter typen stöds inte.|  
|Parameter typen{0}som inte stöds har angetts.|  


## <a name="error-0106"></a>Fel 0106  
 Genereras när en modul definitions fil definierar en indatatyp som inte stöds  
  
 Det här felet i Azure Machine Learning skapas när typen för en indataport i en XML-definition för en anpassad modul inte matchar en typ som stöds.  
  
**Lösning:** Kontrol lera att typ egenskapen för ett indata-element i XML-definitions filen för anpassad modul är en typ som stöds.  
  
|Undantags meddelanden|  
|------------------------|  
|Indatatypen stöds inte.|  
|Indatatypen som inte stöds{0}anges.|  


## <a name="error-0107"></a>Fel 0107  
 Genereras när en modul definitions fil definierar en utdatatyp som inte stöds  
  
 Det här felet i Azure Machine Learning skapas när typen för en utdataport i en XML-definition för en anpassad modul inte matchar en typ som stöds.  
  
**Lösning:** Kontrol lera att typ egenskapen för ett utdata-element i XML-definitions filen för den anpassade modulen är en typ som stöds.  
  
|Undantags meddelanden|  
|------------------------|  
|Utdatatypen stöds inte.|  
|Den utdatatyp som inte stöds{0}anges.|  


## <a name="error-0108"></a>Fel 0108  
 Genereras när en modul definitions fil definierar fler indata-eller utgående portar än vad som stöds  
  
 Det här felet i Azure Machine Learning skapas när för många indata-eller utgående portar definieras i en XML-definition för en anpassad modul.  
  
**Lösning:** Kontrollerar att det maximala antalet portar för indata och utdata som definieras i XML-definitionen för anpassade moduler inte överskrider det högsta antalet portar som stöds.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet indata-eller utgående portar som stöds har överskridits.|  
|Antalet{0}-portar som stöds överskreds. Det högsta tillåtna antalet{0}-portar är{1}.| 

## <a name="error-0109"></a>Fel 0109  
 Genereras när en modul definitions fil definierar en kolumn väljare felaktigt  
  
 Det här felet i Azure Machine Learning skapas när syntaxen för ett kolumn väljare-argument innehåller ett fel i en anpassad XML-definition för modul.  
  
**Lösning:** Det här felet skapas när syntaxen för ett kolumn väljare-argument innehåller ett fel i en anpassad XML-definition för modul.  
  
|Undantags meddelanden|  
|------------------------|  
|Syntaxen för kolumn väljaren stöds inte.|  
  

## <a name="error-0110"></a>Fel 0110  
 Genereras när en modul definitions fil definierar en kolumn väljare som refererar till ett obefintligt indataports-ID  
  
 Det här felet i Azure Machine Learning skapas när egenskapen *portId* i elementet Properties för ett arg av typen ColumnPicker inte matchar ID-värdet för en indataport.  
  
**Lösning:** Kontrol lera att egenskapen portId matchar ID-värdet för en indataport som definierats i XML-definitionen för den anpassade modulen.  
  
|Undantags meddelanden|  
|------------------------|  
|Kolumn väljaren refererar till ett obefintligt indataports-ID.|  
|Kolumn väljaren refererar till en obefintlig indataports-ID{0}.|  
  

## <a name="error-0111"></a>Fel 0111  
 Genereras när en modul definitions fil definierar en ogiltig egenskap  
  
 Det här felet i Azure Machine Learning skapas när en ogiltig egenskap tilldelas ett element i XML-definitionen för den anpassade modulen.  
  
**Lösning:** Kontrol lera att egenskapen stöds av elementet för den anpassade modulen.  
  
|Undantags meddelanden|  
|------------------------|  
|Egenskaps definitionen är ogiltig.|  
|Egenskaps definitionen{0}är ogiltig.|  
  

## <a name="error-0112"></a>Fel 0112  
 Genereras när en modul definitions fil inte kan parsas  
  
 Det här felet i Azure Machine Learning skapas när det finns ett fel i XML-format som förhindrar att XML-definitionen för anpassad modul parsas som en giltig XML-fil.  
  
**Lösning:** Se till att varje element öppnas och stängs korrekt. Kontrol lera att det inte finns några fel i XML-formateringen.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att parsa modul definitions filen.|  
|Det gick inte att parsa modulens definitions fil{0}.|  
  

## <a name="error-0113"></a>Fel 0113  
 Genereras när en modul definitions fil innehåller fel.  
  
 Det här felet i Azure Machine Learning skapas när XML-definitions filen för den anpassade modulen kan parsas, men innehåller fel, till exempel definition av element som inte stöds av anpassade moduler.  
  
**Lösning:** Kontrol lera att definitions filen för den anpassade modulen definierar element och egenskaper som stöds av anpassade moduler.  
  
|Undantags meddelanden|  
|------------------------|  
|Modul definitions filen innehåller fel.|  
|Modul definitions filen{0}innehåller fel.|  
|Modul definitions filen{0}innehåller fel. {1}|  
  

## <a name="error-0114"></a>Fel 0114  
 Uppstod när en anpassad modul skulle skapas.  
  
 Det här felet i Azure Machine Learning skapas när en anpassad version av modulen Miss lyckas. Detta inträffar när en eller flera anpassade module-relaterade fel påträffas när den anpassade modulen läggs till. De ytterligare felen rapporteras i det här fel meddelandet.  
  
**Lösning:** Lös felen som rapporteras i det här undantags meddelandet.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att bygga den anpassade modulen.|  
|Skapande av anpassade moduler misslyckades med fel: {0}|  
  

## <a name="error-0115"></a>Fel 0115  
 Genereras när standard skriptet för en anpassad modul har ett fil namns tillägg som inte stöds.  
  
 Det här felet uppstår i Azure Machine Learning när du anger ett skript för en anpassad modul som använder ett okänt fil namns tillägg.  
  
**Lösning:** Kontrol lera fil formatet och fil namns tillägget för alla skriptfiler som ingår i den anpassade modulen.  
  
|Undantags meddelanden|  
|------------------------|  
|Omfattningen stöds inte för standard skriptet.|  
|Fil utsträcknings {0} som inte stöds för standard skriptet.|  
  

## <a name="error-0121"></a>Fel 0121  
 Utlöstes när SQL-skrivningar Miss lyckas eftersom tabellen är lässkyddad  
  
 Det här felet i Azure Machine Learning skapas när du använder modulen [Exportera data](export-data.md) för att spara resultaten i en tabell i en SQL-databas, och det går inte att skriva till tabellen. Normalt visas det här felet om modulen [Exportera data](export-data.md) upprättar en anslutning till SQL Server-instansen, men inte kan skriva innehållet i Azure ml-datauppsättningen till tabellen.  
  
**Lösning**
 - Öppna fönstret Egenskaper för modulen [Exportera data](export-data.md) och kontrol lera att databas-och tabell namnen har angetts korrekt. 
 - Granska schemat för den data uppsättning som du exporterar och se till att data är kompatibla med mål tabellen.
 - Kontrol lera att SQL-inloggningen som är associerad med användar namnet och lösen ordet har behörighet att skriva till tabellen. 
 - Om undantaget innehåller ytterligare fel information från SQL Server använder du den informationen för att göra ändringar.  
  
|Undantags meddelanden|  
|------------------------|  
|Ansluten till servern, det går inte att skriva till tabellen.|  
|Det går inte att skriva till SQL-tabell: {0}|  


## <a name="error-0122"></a>Fel 0122  
 Undantag uppstår om flera vikt kolumner har angetts och bara en tillåts.  
  
 Det här felet uppstår i Azure Machine Learning när för många kolumner har valts som vikt kolumner.  
  
**Lösning:** Granska indata-datauppsättningen och dess metadata. Se till att endast en kolumn innehåller vikter.  
  
|Undantags meddelanden|  
|------------------------|  
|Flera vikt kolumner har angetts.|  


## <a name="error-0123"></a>Fel 0123  
 Undantag uppstår om kolumnen med vektorer har angetts för att ha etiketten kolumn.  
  
 Felet i Azure Machine Learning uppstår om du använder en Vector som etikett kolumn.  
  
**Lösning:** Ändra kolumnens data format om det behövs eller Välj en annan kolumn.  
  
|Undantags meddelanden|  
|------------------------|  
|Kolumn med vektorer har angetts som etikett kolumn.|  


## <a name="error-0124"></a>Fel 0124  
 Ett undantag uppstår om icke-numeriska kolumner anges som vikt kolumn.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|En icke-numerisk kolumn har angetts som vikt kolumn.|  
  


## <a name="error-0125"></a>Fel 0125  
 Utlöstes när schemat för flera data uppsättningar inte matchar.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Data uppsättningens schema stämmer inte överens.|  


## <a name="error-0126"></a>Fel 0126  
 Undantag uppstår om användaren anger en SQL-domän som inte stöds i Azure ML.  
  
 Det här felet skapas när användaren anger en SQL-domän som inte stöds i Azure Machine Learning. Du får det här felet om du försöker ansluta till en databas server i en domän som inte är vit listas. För närvarande är de tillåtna SQL-domänerna: ". database.windows.net", ". cloudapp.net" eller ". database.secure.windows.net". Det vill säga att servern måste vara en Azure SQL-Server eller en server på en virtuell dator i Azure.  
  
**Lösning:** Gå tillbaka i modulen. Verifiera att SQL Database-servern tillhör en av de godkända domänerna:  
  
-   . database.windows.net  
  
-   . cloudapp.net  
  
-   . database.secure.windows.net  
  
|Undantags meddelanden|  
|------------------------|  
|SQL-domänen stöds inte.|  
|SQL-domänens {0} stöds för närvarande inte i Azure ML|  
  

## <a name="error-0127"></a>Fel 0127  
 Bild punkts storleken överskrider den tillåtna gränsen  
  
 Det här felet uppstår om du läser bilder från en avbildnings data uppsättning för klassificering och avbildningarna är större än vad modellen kan hantera.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Undantags meddelanden|  
|------------------------|  
|Bild punkts storleken överskrider den tillåtna gränsen.|  
|Bild punkts storleken i filen{0}överskrider den tillåtna gränsen:{1}|  


## <a name="error-0128"></a>Fel 0128  
 Antalet villkorsstyrda sannolikheter för kategoriska-kolumner överskrider gränsen.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Antalet villkorsstyrda sannolikheter för kategoriska-kolumner överskrider gränsen.|  
|Antalet villkorsstyrda sannolikheter för kategoriska-kolumner överskrider gränsen. Kolumnerna "{0}" och "{1}" är det problematiska paret.|  


## <a name="error-0129"></a>Fel 0129  
 Antalet kolumner i data uppsättningen överskrider den tillåtna gränsen.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Antalet kolumner i data uppsättningen överskrider den tillåtna gränsen.|  
|Antalet kolumner i data uppsättningen i{0}överskrider tillåtet.|  
|Antalet kolumner i data uppsättningen i{0}överskrider den tillåtna gränsen på{1}.|  
|Antalet kolumner i data uppsättningen i{0}överskrider tillåtet{1}gräns på{2}.|  
## <a name="error-0130"></a>Fel 0130  
 Undantag inträffar när alla rader i träning-datauppsättningen innehåller saknade värden.  
  
 Detta inträffar när en kolumn i träning-datauppsättningen är tom.  
  
**Lösning:** Använd modulen [Rensa data som saknas](clean-missing-data.md) för att ta bort kolumner med alla saknade värden.  
  
|Undantags meddelanden|  
|------------------------|  
|Alla rader i tränings data uppsättningen innehåller saknade värden.  Överväg att använda modulen rensa data som saknas för att ta bort saknade värden.|  
 

## <a name="error-0131"></a>Fel 0131  
 Undantag uppstår om en eller flera data uppsättningar i en zip-fil inte kan zippas och registreras korrekt  
  
 Det här felet uppstår när en eller flera data uppsättningar i en zip-fil inte kan avzippas och läsas korrekt. Du får det här felet om uppackningen Miss lyckas eftersom själva zip-filen eller en av filerna i filen är skadad eller om det uppstår ett systemfel vid försök att packa upp och expandera en fil.  
  
**Lösning:** Använd informationen i fel meddelandet för att avgöra hur du ska fortsätta.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att överföra zippade data uppsättningar|  
|Zippade data uppsättnings {0} misslyckades med följande meddelande: {1}|  
|Zippade data uppsättnings {0} misslyckades med ett {1}-undantag med meddelande: {2}|  
  

## <a name="error-0132"></a>Fel 0132  
 Inget fil namn har angetts för uppackning; flera filer hittades i zip-filen.  
  
 Det här felet skapas när inget fil namn har angetts för uppackning. flera filer hittades i zip-filen. Du får det här felet om. zip-filen innehåller fler än en komprimerad fil, men du angav inte någon fil för extrahering i text rutan **data uppsättning att packa upp** i rutan **egenskap** i modulen. För närvarande kan endast en fil extraheras varje gången modulen körs.  
  
**Lösning:** Fel meddelandet innehåller en lista över de filer som finns i. zip-filen. Kopiera namnet på den önskade filen och klistra in den i text rutan **data uppsättning att packa upp** .  
  
|Undantags meddelanden|  
|------------------------|  
|Zip-filen innehåller flera filer. Du måste ange filen som ska expanderas.|  
|Filen innehåller fler än en fil. Ange den fil som ska expanderas. Följande filer hittades: {0}|  
  

## <a name="error-0133"></a>Fel 0133  
 Det gick inte att hitta den angivna filen i zip-filen  
  
 Det här felet skapas när fil namnet som angavs i fältet **data uppsättning till avpackas** i **egenskaps** fönstret inte matchar namnet på någon fil som finns i. zip-filen. De vanligaste orsakerna till det här felet är ett skrivfel eller att söka i fel Arkiv filen för filen som ska expanderas.  
  
**Lösning:** Gå tillbaka i modulen. Om namnet på filen som du vill expandera visas i listan med filer, kopierar du fil namnet och klistrar in det i rutan **data uppsättning till packa upp** . Om du inte ser det önskade fil namnet i listan kontrollerar du att du har rätt. zip-fil och rätt namn för önskad fil.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att hitta den angivna filen int zip-filen.|  
|Det gick inte att hitta den angivna filen. Hittade följande fil (er): {0}|  
  

## <a name="error-0134"></a>Fel 0134
Ett undantag inträffar när etikett kolumnen saknas eller har otillräckligt antal etiketterade rader.  
  
Det här felet uppstår när modulen kräver en etikett kolumn, men du inte inkluderade någon i kolumn urvalet, eller så saknas kolumnen etikett för många värden.

Felet kan också uppstå när en tidigare åtgärd ändrar data uppsättningen så att otillräckliga rader är tillgängliga för en underordnad åtgärd. Anta till exempel att du använder ett uttryck i modulen **partition och exempel** för att dela upp en data uppsättning efter värden. Om inga matchningar hittas för ditt uttryck skulle en av de data uppsättningar som skapas från partitionen vara tom.

Lösning: 

 Om du lägger till en etikett kolumn i kolumn valet men inte känns igen, använder du modulen [Redigera metadata](edit-metadata.md) för att markera den som en etikett kolumn.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Sedan kan du använda modulen [Rensa data som saknas](clean-missing-data.md) för att ta bort rader med värden som saknas i kolumnen etikett. 

 Kontrol lera dina indata-datauppsättningar för att se till att de innehåller giltiga data, och tillräckligt många rader för att uppfylla kraven för åtgärden. Många algoritmer genererar ett fel meddelande om de kräver vissa minsta antal rader med data, men data innehåller bara några rader eller bara en rubrik.
  
|Undantags meddelanden|
|------------------------|
|Ett undantag inträffar när etikett kolumnen saknas eller har otillräckligt antal etiketterade rader.|  
|Undantag inträffar när etikett kolumnen saknas eller har färre än {0} etiketterade rader|  
  

## <a name="error-0135"></a>Fel 0135  
 Endast centroid-baserade kluster stöds.  
  
**Lösning:** Du kan stöta på det här fel meddelandet om du har försökt utvärdera en kluster modell som baseras på en anpassad klustrad algoritm som inte använder centroids för att initiera klustret.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Undantags meddelanden|  
|------------------------|  
|Endast centroid-baserade kluster stöds.|  
  

## <a name="error-0136"></a>Fel 0136  
 Inget fil namn returnerades. Det gick inte att bearbeta filen på grund av ett resultat.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Inget fil namn returnerades. Det gick inte att bearbeta filen på grund av ett resultat.|  
  

## <a name="error-0137"></a>Fel 0137  
 Azure Storage SDK påträffade ett fel vid konvertering mellan tabell egenskaper och data mängds kolumner vid läsning eller skrivning.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Konverterings fel mellan Azure Table Storage-egenskap och data uppsättnings kolumn.|  
|Konverterings fel mellan Azure Table Storage-egenskap och data uppsättnings kolumn. Ytterligare information: {0}|  

## <a name="error-0138"></a>Fel 0138  
 Minnet har tömts, det går inte att slutföra körningen av modulen. Nedsampling av data uppsättningen kan hjälpa till att minska problemet.  
  
 Felet uppstår när modulen som kör kräver mer minne än vad som är tillgängligt i Azure-behållaren. Detta kan inträffa om du arbetar med en stor data uppsättning och den aktuella åtgärden inte ryms i minnet.  
  
**Lösning:** Om du försöker läsa en stor data uppsättning och åtgärden inte kan slutföras kan det hjälpa att göra en nedsampling av data uppsättningen.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Undantags meddelanden|  
|------------------------|  
|Minnet har tömts, det går inte att slutföra körningen av modulen.|  
  

## <a name="error-0139"></a>Fel 0139  
 Ett undantag inträffar när det inte går att konvertera en kolumn till en annan typ.  
  
 Det här felet uppstår i Azure Machine Learning när du försöker konvertera en kolumn till en annan datatyp, men den typen stöds inte av den aktuella åtgärden eller av modulen.  
  
 Felet kan också visas när en modul försöker implicit konvertera data till att uppfylla kraven i den aktuella modulen, men konverteringen är inte möjlig.  
  
**Lösning**

1. Granska indata och ta reda på den exakta data typen för den kolumn som du vill använda och data typen för den kolumn som genererar felet. Ibland kanske du tror att data typen är korrekt, men att det är en åtgärd som har ändrat data typen eller användningen av en kolumn. Använd modulen [Redigera metadata](edit-metadata.md) för att återställa kolumnens metadata till dess ursprungliga tillstånd. 
2. Titta på hjälp sidan för modulen för att kontrol lera kraven för den angivna åtgärden. Bestäm vilka data typer som stöds av den aktuella modulen och vilka värde intervall som stöds. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Överväg om det är möjligt att konvertera eller omvandla kolumnen till en annan datatyp. I följande moduler får du stor flexibilitet och kraft för att ändra data: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Kör Python-skript](execute-python-script.md).  

> [!NOTE]
> Fungerar det fortfarande inte? Överväg att ge ytterligare feedback om problemet för att hjälpa oss att utveckla bättre fel söknings vägledning. Skicka bara feedback på den här sidan och ange namnet på den modul som orsakade felet och data typs konverteringen som misslyckades.
  
|Undantags meddelanden|  
|------------------------|  
|Tillåts inte konvertering.|  
|Det gick inte att konvertera: {0}.|  
|Det gick inte att konvertera: {0}på rad {1}.|  
|Det gick inte att konvertera kolumnen av typen {0} till kolumn av typen {1} på rad {2}.|  
|Det gick inte att konvertera kolumnen "{2}" av typen {0} till kolumnen av typen {1} på rad {3}.|  
|Det gick inte att konvertera kolumnen "{2}" av typen {0} till kolumnen "{3}" av typen {1} på rad {4}.| 

## <a name="error-0140"></a>Fel 0140  
 Ett undantag inträffar om det skickade kolumn uppsättnings argumentet inte innehåller andra kolumner förutom kolumnen etikett.  
  
 Det här felet uppstår om du har anslutit en data uppsättning till en modul som kräver flera kolumner, inklusive funktioner, men som du har angett endast kolumnen etikett.  
  
**Lösning:** Välj minst en funktions kolumn som ska tas med i data uppsättningen.  
  
|Undantags meddelanden|  
|------------------------|  
|Den angivna kolumn uppsättningen innehåller inte andra kolumner förutom kolumnen etikett.|  
  

## <a name="error-0141"></a>Fel 0141  
 Undantag uppstår om antalet markerade numeriska kolumner och unika värden i kolumnerna kategoriska och String är för litet.  
  
 Det här felet uppstår i Azure Machine Learning när det inte finns tillräckligt många unika värden i den markerade kolumnen för att utföra åtgärden.  
  
**Lösning:** Vissa åtgärder utför statistiska åtgärder på funktions-och kategoriska kolumner, och om det inte finns tillräckligt med värden kan åtgärden Miss lyckas eller returnera ett ogiltigt resultat. Kontrol lera din data uppsättning för att se hur många värden som finns i kolumnerna Feature och Label, och Fastställ om åtgärden du försöker utföra är statistiskt giltig.  
  
 Om käll data uppsättningen är giltig kan du också kontrol lera om någon överordnad data behandling eller metadata har ändrat data och tagit bort vissa värden.  
  
 Om överordnade åtgärder innefattar delning, sampling eller omsampling, verifierar du att utdata innehåller det förväntade antalet rader och värden.  
  
|Undantags meddelanden|  
|------------------------|  
|Antalet markerade numeriska kolumner och unika värden i kolumnerna kategoriska och String är för litet.|  
|Det totala antalet markerade numeriska kolumner och unika värden i kategoriska-och sträng kolumnerna (för närvarande {0}) måste vara minst {1}|  
  

## <a name="error-0142"></a>Fel 0142  
 Undantag inträffar när systemet inte kan läsa in certifikat för autentisering.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att läsa in certifikatet.|  
|Det gick inte att läsa in certifikat {0}. Dess tumavtryck är {1}.|  
  

## <a name="error-0143"></a>Fel 0143  
 Det går inte att parsa användardefinierad URL som ska vara från GitHub.  
  
 Felet i Azure Machine Learning uppstår när du anger en ogiltig URL och modulen kräver en giltig GitHub-URL.  
  
**Lösning:** Verifiera att URL: en refererar till en giltig GitHub-lagringsplats. Andra webbplats typer stöds inte.  
  
|Undantags meddelanden|  
|------------------------|  
|URL: en är inte från github.com.|  
|URL: en är inte från github.com: {0}|  

## <a name="error-0144"></a>Fel 0144  
 Den förväntade GitHub saknas i den förväntade webb adressen för användaren.  
  
 Det här felet uppstår i Azure Machine Learning när du anger en GitHub fil källa med ett ogiltigt URL-format.  
  
**Lösning:** Kontrol lera att URL: en för GitHub-lagringsplatsen är giltig och slutar med \blob\ eller \tree\\.  
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att parsa GitHub-URL.|  
|Det går inte att parsa GitHub-URL (förväntar ' \blob\\' eller ' \tree\\' efter lagrings platsens namn): {0}|  

## <a name="error-0145"></a>Fel 0145  
 Det går inte att skapa någon anledning till replikerings katalogen.  
  
 Det här felet uppstår i Azure Machine Learning när modulen inte kan skapa den angivna katalogen.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Det går inte att skapa en replikeringspartner.|  
  

## <a name="error-0146"></a>Fel 0146  
 När användarfiler är zippade i den lokala katalogen kan den kombinerade sökvägen vara för lång.  
  
 Det här felet uppstår i Azure Machine Learning när du extraherar filer men vissa fil namn är för långa för att zippas.  
  
**Lösning:** Redigera fil namnen, till exempel att kombinerade sökvägen och fil namnet inte är längre än 248 tecken.  
  
|Undantags meddelanden|  
|------------------------|  
|Sökvägen till replikeringen är längre än 248 tecken, förkorta skriptets namn eller sökväg.|  

## <a name="error-0147"></a>Fel 0147  
 Det gick inte att hämta saker från GitHub av någon anledning  
  
 Det här felet uppstår i Azure Machine Learning när du inte kan läsa eller hämta de angivna filerna från GitHub.  
  
**Lösning:** Problemet kan vara tillfälligt. Du kan försöka komma åt filerna vid ett senare tillfälle. Eller kontrol lera att du har de behörigheter som krävs och att källan är giltig.  
  
|Undantags meddelanden|  
|------------------------|  
|Åtkomst fel för GitHub.|  
|Åtkomst fel för GitHub. {0}|  
  

## <a name="error-0148"></a>Fel 0148  
 Obehöriga åtkomst problem vid extrahering av data eller skapande av katalog.  
  
 Det här felet uppstår i Azure Machine Learning när du försöker skapa en katalog eller läsa data från lagringen men inte har de behörigheter som krävs.  
  
**Lösning**
  
|Undantags meddelanden|  
|------------------------|  
|Obehörigt åtkomst undantag vid extrahering av data.|  
  

## <a name="error-0149"></a>Fel 0149  
 Användar filen finns inte i GitHub-paketet.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att hitta den angivna filen.  
  
Lösning: 
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att hitta GitHub-filen.|  
|Det gick inte att hitta GitHub-filen.: {0}|  
  

## <a name="error-0150"></a>Fel 0150  
 Det gick inte att zippa upp skripten som kommer från användar paketet, förmodligen på grund av en kollision med GitHub-filer.  
  
 Det här felet uppstår i Azure Machine Learning när det inte går att extrahera ett skript, vanligt vis när det finns en befintlig fil med samma namn.  
  
Lösning:
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att packa upp paketet. möjligt namn konflikt med GitHub-filer.|  
  

## <a name="error-0151"></a>Fel 0151  
 Ett fel uppstod vid skrivning till moln lagring. Kontrol lera URL: en.  
  
 Det här felet uppstår i Azure Machine Learning när modulen försöker skriva data till moln lagring, men URL: en är inte tillgänglig eller ogiltig.  
  
Lösning: kontrol lera webb adressen och kontrol lera att den är skrivbar.  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att skriva till moln lagring (eventuellt en felaktig URL).|  
|Det gick inte att skriva till moln lagring: {0}. Kontrol lera URL: en.|  
  
## <a name="error-0152"></a>Fel 0152  
 Azures moln typ angavs felaktigt i modulens kontext.  
  
|Undantags meddelanden|  
|------------------------|  
|Felaktig moln typ i Azure|  
|Felaktig typ av Azure-moln: {0}|  
  
## <a name="error-0153"></a>Fel 0153  
 Den angivna lagrings slut punkten är ogiltig.  
  
|Undantags meddelanden|  
|------------------------|  
|Felaktig moln typ i Azure|  
|Felaktig lagrings slut punkt: {0}|  

## <a name="error-0154"></a>Fel 0154  
 Det gick inte att matcha det angivna Server namnet  
  
|Undantags meddelanden|  
|------------------------|  
|Det gick inte att matcha det angivna Server namnet|  
|Det gick inte att matcha den angivna servern {0}. documents.azure.com|

## <a name="error-0155"></a>Fel 0155  
 DocDb-klienten utlöste ett undantag  
  
|Undantags meddelanden|  
|------------------------|  
|DocDb-klienten utlöste ett undantag|  
|DocDb-klient: {0}|

## <a name="error-0156"></a>Fel 0156  
 Felaktigt svar för HCatalog-Server.  
  
|Undantags meddelanden|  
|------------------------|  
|Felaktigt svar för HCatalog-Server. Kontrol lera att alla tjänster körs.|  
|Felaktigt svar för HCatalog-Server. Kontrol lera att alla tjänster körs. Felinformation: {0}|

## <a name="error-0157"></a>Fel 0157  
 Ett fel uppstod vid läsning från Azure Cosmos DB på grund av inkonsekventa eller olika dokument scheman. Läsaren kräver att alla dokument har samma schema.  
  
|Undantags meddelanden|  
|------------------------|  
|Identifierade dokument med olika scheman. Se till att alla dokument har samma schema|

## <a name="error-1000"></a>Fel 1000  
Internt biblioteks undantag.  
  
Det här felet tillhandahålls för att samla in andra fel som inte hanteras av en intern motor. Därför kan orsaken till det här felet vara olika beroende på vilken modul som orsakade felet.  
  
För att få mer hjälp rekommenderar vi att du skickar det detaljerade meddelandet som medföljer felet till Azure Machine Learning-forumet, tillsammans med en beskrivning av scenariot, inklusive data som används som indata. Den här feedbacken hjälper oss att prioritera fel och identifiera de viktigaste problemen för ytterligare arbete.  
  
|Undantags meddelanden|  
|------------------------|  
|Biblioteks undantag.|  
|Biblioteks undantag: {0}|  
|undantag för {0} bibliotek: {1}|  
