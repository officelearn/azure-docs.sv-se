---
title: Vad är ML-pipelines
titleSuffix: Azure Machine Learning
description: I den här artikeln får du lära dig fördelarna med dator inlärnings-pipelinen (ML) som du kan bygga med Azure Machine Learning SDK för python. Maskin inlärnings pipeliner används av data forskare för att bygga, optimera och hantera sina Machine Learning-arbetsflöden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 84a01597570a488652e3db2345bdf68b52d4bf5b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973585"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Vad är Azure Machine Learning pipelines?

Med Azure Machine Learning pipelines kan du skapa arbets flöden i dina Machine Learning-projekt. De här arbets flödena har ett antal fördelar: 

+ Enkelhet
+ Hastighet
+ Repeterbarhet
+ Flexibilitet
+ Versions hantering och spårning
+ Modulariteten 
+ Kvalitetssäkring
+ Kostnads kontroll

Dessa förmåner blir viktiga så snart ditt Machine Learning-projekt flyttas bortom ren utforskning och till iteration. Det kan vara värdefullt att använda pipeliner med en enda steg. Machine Learning-projekt är ofta i komplext tillstånd, och det kan vara en avsättning för att göra den exakta processen för ett enskilt arbets flöde en trivial process.

Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md).

![Maskin inlärnings pipeliner i Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Vilken Azure pipeline-teknik ska jag använda?

Azure-molnet tillhandahåller flera andra pipelines, var och en med olika ändamål. I följande tabell visas de olika pipelinen och vad de används för:

| Pipeline | Vad verktyget gör | Kanoniskt rör |
| ---- | ---- | ---- |
| Azure Machine Learning pipelines | Definierar återanvändbara Machine Learning-arbetsflöden som kan användas som en mall för dina maskin inlärnings scenarier. | Data > modell |
| [Azure Data Factory-pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Grupperar data flyttnings-, omvandlings-och kontroll aktiviteter som krävs för att utföra en uppgift.  | Data-> data |
| [Azure-pipeline](https://azure.microsoft.com/services/devops/pipelines/) | Kontinuerlig integrering och leverans av ditt program till alla plattformar/moln  | Code-> App/Service |

## <a name="what-can-azure-ml-pipelines-do"></a>Vad kan Azure ML-pipeliner göra?

En Azure Machine Learning pipeline är ett oberoende körbart arbets flöde för en fullständig Machine Learning-uppgift. Under aktiviteter kapslas in som en serie steg i pipelinen. En Azure Machine Learning pipeline kan vara lika enkelt som en som anropar ett Python-skript, så det _kan hända_ bara vad som helst. Pipelines _bör_ fokusera på Machine Learning-uppgifter som:

+ Förberedelse av data, inklusive import, validering och rensning, munging och transformering, normalisering och mellanlagring
+ Konfiguration av utbildning, inklusive parametera argument, Sök vägar och loggning/rapporterings konfiguration
+ Utbildning och validering på ett effektivt och upprepat sätt, vilket kan omfatta att ange vissa data del mängder, olika maskin varu beräknings resurser, distribuerad bearbetning och förlopps övervakning
+ Distribution, inklusive versions hantering, skalning, etablering och åtkomst kontroll 

Oberoende steg gör det möjligt för flera data forskare att arbeta med samma pipeline samtidigt utan beskattnings beräknings resurser. Separata steg gör det också enkelt att använda olika beräknings typer/storlekar för varje steg.

Efter att pipelinen har utformats finns det ofta mer fin justering av pipelinens inlärnings slinga. När du kör en pipeline igen hoppar körningen till de steg som måste köras igen, till exempel ett uppdaterat utbildnings skript. Steg som inte behöver köras igen hoppas över. Samma analys gäller för oförändrade skript som används för att slutföra steget. Den här åter användnings funktionen hjälper till att undvika att köra kostsamma och tids krävande steg som data inmatning och omvandling om underliggande data inte har ändrats.

Med Azure Machine Learning kan du använda olika verktyg och ramverk, till exempel PyTorch eller TensorFlow, för varje steg i din pipeline. Azure koordinerar de olika [beräknings målen](concept-azure-machine-learning-architecture.md) som du använder, så att mellanliggande data kan delas med de underordnade beräknings målen.

Du kan [spåra måtten för dina pipelines experiment](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) direkt i Azure Portal eller i [landnings sidan för din arbets yta (för hands version)](https://ml.azure.com). När en pipeline har publicerats kan du konfigurera en REST-slutpunkt, vilket gör att du kan köra pipelinen igen från valfri plattform eller stack.

I korthet kan alla komplexa uppgifter i Machine Learning-livscykeln bli hjälpte med pipeliner. Andra Azure pipeline-tekniker har sina egna styrkor, till exempel [Azure Data Factory pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) för att arbeta med data och [Azure-pipeliner](https://azure.microsoft.com/services/devops/pipelines/) för kontinuerlig integrering och distribution. Men om fokus är maskin inlärning är Azure Machine Learning pipelines förmodligen det bästa valet för arbets flödes behoven. 

## <a name="what-are-azure-ml-pipelines"></a>Vad är Azure ML-pipelines?

En Azure ML-pipeline utför ett fullständigt logiskt arbets flöde med en ordnad sekvens av steg. Varje steg är en diskret bearbetnings åtgärd. Pipelines körs i samband med ett Azure Machine Learning [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

I det allra tidiga skedet av ett ML-projekt är det bra att ha en enda Jupyter Notebook eller Python-skript som gör allt arbete i Azure-arbetsytan och resurs konfigurationen, förberedelse av data, kör konfiguration, utbildning och validering. Men precis som funktioner och klasser kan snabbt bli bättre för ett enda tvingande kodblock, och ML-arbetsflöden blir snabbt bättre än en monolitisk-anteckningsbok eller ett skript. 

Med hjälp av ML-aktiviteter stöder pipelines den dator vetenskap som är nödvändig för att en komponent ska kunna "göra (endast) en sak." Det är tydligt viktigt att Project lyckas när programmering i team används, men även när ett litet ML-projekt kräver separata uppgifter, var och en med en bra komplexitet. Uppgifterna omfattar: arbets ytans konfiguration och data åtkomst, förberedelse av data, modell definition och konfiguration och distribution. Även om utdata från en eller flera aktiviteter utgör indata till en annan, är de exakta implementerings detaljerna för en aktivitet, vid bästa, irrelevanta störande i nästa. I värsta fall kan beräknings statusen för en aktivitet orsaka en bugg i en annan. 

### <a name="analyzing-dependencies"></a>Analysera beroenden

Många programmerings eko system har verktyg som dirigerar resurs-, biblioteks-eller kompilerings beroenden. Dessa verktyg använder vanligt vis fil-tidsstämplar för att beräkna beroenden. När en fil ändras, uppdateras bara den och dess beroenden (hämtas, kompileras om eller paketeras). Azure ML-pipeliner utökar det här konceptet dramatiskt. Precis som traditionella build-verktyg beräknar pipelines beroenden mellan stegen och utför bara de nödvändiga Omberäkningarna. 

Beroende analysen i Azure ML-pipelines är mer sofistikerade än enkla tidsstämplar. Varje steg kan köras i en annan maskin-och program varu miljö. Förberedelse av data kan vara en tids krävande process men behöver inte köras på maskin vara med kraftfulla GPU: er, vissa steg kan kräva en OS-specifik program vara, du kanske vill använda distribuerad utbildning och så vidare. Kostnaderna för optimering av resurser kan vara betydande, men det kan vara mycket viktigt att Juggle alla olika variationer i maskin-och program varu resurser manuellt. Det är ännu svårare att göra allt detta utan att behöva göra några fel i data som du överför mellan stegen. 

Pipelines löser det här problemet. Azure Machine Learning dirigerar automatiskt alla beroenden mellan stegen i pipeline. Den här dirigeringen kan omfatta att sätta upp och ned Docker-avbildningar, koppla och koppla från beräknings resurser och flytta data mellan stegen på ett konsekvent och automatiskt sätt.

### <a name="reusing-results"></a>Återanvända resultat

Dessutom kan utdata från ett steg, om du väljer, återanvändas. Om du anger åter användning som en möjlighet och det inte finns några uppströms beroenden som utlöser omberäkningen, kommer pipelin tjänsten att använda en cachelagrad version av stegets resultat. Sådan åter användning kan avsevärt minska utvecklings tiden. Om du har en komplex data förberedelse uppgift, kör du förmodligen om den oftare än vad som är absolut nödvändigt. Pipelines befriar dig från att oroa dig: om det behövs kommer steget att köras, om det inte är det.

Alla dessa beroende analyser, dirigering och aktivering hanteras av Azure Machine Learning när du instansierar ett [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) -objekt, skickar det till ett `Experiment`och anropar `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Samordna de steg som ingår

När du skapar och kör ett `Pipeline`-objekt inträffar följande steg på hög nivå:

+ För varje steg beräknar tjänsten kraven för:
    + Maskin varu beräknings resurser
    + OS-resurser (Docker avbildningar)
    + Program varu resurser (Conda/virtuell miljö-beroenden)
    + Indata 
+ Tjänsten fastställer beroenden mellan steg, vilket resulterar i ett dynamiskt körnings diagram
+ När varje nod i körnings diagrammet körs:
    + Tjänsten konfigurerar nödvändig maskin-och program varu miljö (kanske återanvänder befintliga resurser)
    + Steget körs, vilket ger information om loggning och övervakning till dess innehåll ande `Experiment`-objekt
    + När steget är klart förbereds utmatningarna som indata till nästa steg och/eller skrivs till lagring
    + Resurser som inte längre behövs har slutförts och kopplats från

![Pipeline-steg](media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Skapa pipelines med python SDK

I [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)är en pipeline ett python-objekt som definierats i modulen `azureml.pipeline.core`. Ett [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) -objekt innehåller en ordnad sekvens av ett eller flera [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) -objekt. Klassen `PipelineStep` är abstrakt och de faktiska stegen är underklasser som [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)eller [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) -klassen innehåller en återanvändbara sekvens med steg som kan delas mellan pipeliner. En `Pipeline` körs som en del av ett `Experiment`.

En Azure ML-pipeline är kopplad till en Azure Machine Learning arbets yta och ett steg i pipeline-steget är associerat med ett beräknings mål som är tillgängligt i arbets ytan. Mer information finns i [skapa och hantera Azure Machine Learning arbets ytor i Azure Portal](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) eller [Vad är beräknings mål i Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).

I Azure Machine Learning är ett beräknings mål den miljö där en ML-fas inträffar. Program varu miljön kan vara en virtuell fjärrdator, Azure Machine Learning Compute, Azure Databricks, Azure Batch och så vidare. Maskin varu miljön kan också variera mycket, beroende på GPU-stöd, minne, lagring och så vidare. Du kan ange beräknings mål för varje steg, vilket ger dig detaljerad kontroll över kostnaderna. Du kan använda mer eller mindre kraftfulla resurser för det aktuella åtgärds-, data volym-och prestanda behov för ditt projekt. 

## <a name="building-pipelines-with-the-designer"></a>Skapa pipelines med designern

Utvecklare som föredrar en visuell design yta kan använda Azure Machine Learning designer för att skapa pipeliner. Du kan komma åt det här verktyget från **Designer** -valet på arbets ytans start sida.  Med designern kan du dra och släppa steg till design ytan. För snabb utveckling kan du använda befintliga moduler i hela spektrumet av ML-uppgifter. befintliga moduler behandlar allt från datatransformering till algoritm val för att träna distribution. Du kan också skapa en helt anpassad pipeline genom att kombinera dina egna steg som definieras i Python-skript.

När du visuellt utformar pipeliner visas indata och utdata för ett steg på ett synligt sätt. Du kan dra och släppa data anslutningar så att du snabbt kan förstå och ändra data flödet för din pipeline.
 
![Exempel på Azure Machine Learning designer](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Förstå körnings diagrammet

Stegen i en pipeline kan ha beroenden i andra steg. Tjänsten Azure ML pipeline gör arbetet med att analysera och dirigera dessa beroenden. Noderna i det resulterande "körnings diagrammet" är bearbetnings steg. Varje steg kan innebära att skapa eller återanvända en viss kombination av maskin vara och program vara, återanvända cachelagrade resultat och så vidare. Tjänstens dirigering och optimering av det här körnings diagrammet kan påskynda en ML-fas och minska kostnaderna. 

Eftersom stegen körs oberoende, måste objekt som innehåller indata och utdata som flödar mellan stegen definieras externt. Detta är rollen för [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)och associerade klasser. Dessa data objekt är associerade med ett data [lager](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) objekt som kapslar in sin lagrings konfiguration. `PipelineStep` Bask Lassen skapas alltid med en `name` sträng, en lista över `inputs`och en lista över `outputs`. Vanligt vis finns det också en lista över `arguments` och det finns ofta en lista över `resource_inputs`. Underklasser har i allmänhet även ytterligare argument (till exempel kräver `PythonScriptStep` fil namn och sökväg till skriptet som ska köras). 

Körnings diagrammet är acykliska, men pipelines kan köras enligt ett återkommande schema och kan köra Python-skript som kan skriva statusinformation till fil systemet, vilket gör det möjligt att skapa komplexa profiler. Om du utformar din pipeline så att vissa steg kan köras parallellt eller asynkront, hanterar Azure Machine Learning transparent analys av beroende analyser och samordningen av fläkt och fläkt. Du behöver vanligt vis inte bekymra dig om körnings diagrammets information, men det är tillgängligt via [pipeline. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) -attributet. 


### <a name="a-simple-python-pipeline"></a>En enkel python-pipeline

Det här kodfragmentet visar de objekt och anrop som behövs för att skapa och köra en grundläggande `Pipeline`:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Kodfragmentet börjar med vanliga Azure Machine Learning objekt, en `Workspace`, en `Datastore`, en [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)och en `Experiment`. Sedan skapar koden de objekt som ska innehålla `input_data` och `output_data`. Matrisen `steps` innehåller ett enda element, en `PythonScriptStep` som ska använda data objekt och köras på `compute_target`. Sedan instansierar koden själva `Pipeline` själva objektet, och skickar i matrisen för arbets ytor och steg. Anropet till `experiment.submit(pipeline)` börjar köra Azure ML-pipeline. Anropet till `wait_for_completion()` block tills pipelinen är slutförd. 

## <a name="best-practices-when-using-pipelines"></a>Bästa metoder när du använder pipelines

Som du kan se är det mycket mer komplicerat att skapa en Azure ML-pipeline än att starta ett skript. Pipelines kräver att några python-objekt konfigureras och skapas. 

Vissa situationer som föreslår användning av en pipeline:

* I en grupp miljö: dela upp ML-aktiviteter i flera oberoende steg så att utvecklare kan arbeta och utveckla sina program oberoende av varandra. 

* Vid en eller nära distribution: Nail ned konfigurationen och Använd schemalagda och händelse drivna åtgärder för att hålla koll på ändringar av data.

* I de tidiga faserna i ett ML-projekt eller som arbetar självständigt: Använd pipelines för att automatisera versionen. Om du har börjat bekymra dig om att återskapa konfigurationen och data bearbetningen innan du implementerar en ny idé, är det en signal som du kan överväga att använda en pipeline för att automatisera arbets flödet. 

Det är enkelt att bli entusiastisk om återanvändandet av cachelagrade resultat, detaljerad kontroll över beräknings kostnader och process isolering, men pipelines har kostnader. Några anti-mönster är:

* Använd pipeliner som enda metod för att avgränsa problem. De inbyggda funktionerna, objekten och modulerna i python är mycket bra för att undvika förvirrande programmerings tillstånd! Ett pipeline-steg är mycket dyrare än ett funktions anrop.

* Tung koppling mellan pipeline-steg. Om omberäkningen av ett beroende steg ofta kräver att du ändrar utdata i ett föregående steg, är det troligt att separata steg är för närvarande mer av en kostnad än en förmån. En annan uppfattning om att stegen är överkopplade är argument till ett steg som inte är data utan flaggor för att styra bearbetningen. 

* Optimering av beräknings resurser för tidigt. Det finns ofta flera steg för förberedelse av data och en kan ofta se "man, här är en plats där jag kan använda en `MpiStep` för parallell programmering, men här är en plats där jag kan använda en `PythonScriptStep` med ett mindre kraftfullt beräknings mål," och så vidare. Och kanske i lång sikt skapa detaljerade steg som kan vara ett bra resultat, särskilt om det finns en möjlighet att använda cachelagrade resultat i stället för att alltid omberäkna. Men pipelines är inte avsedda att ersätta `multiprocessing`-modulen. 

Innan ett projekt får stor eller nära distribution bör pipelinen vara coarser i stället för detaljerade. Om du tänker på ditt ML-projekt i takt med att det rör sig om _steg_ och en pipeline som ett komplett arbets flöde för att flytta dig genom ett visst steg är du på rätt väg. 

## <a name="key-advantages"></a>Viktiga fördelar

De främsta fördelarna med att använda pipeliner för dina Machine Learning-arbetsflöden är:

|Stor fördel|Beskrivning|
|:-------:|-----------|
|**Obevakad&nbsp;körs**|Schemalägg steg som ska köras parallellt eller i följd på ett tillförlitligt och obevakat sätt. Förberedelse av data och modellering kan senaste dagar eller veckor, och pipelines gör att du kan fokusera på andra uppgifter medan processen körs. |
|**Heterogena-beräkning**|Använd flera pipelines som är tillförlitligt koordinerade över heterogena och skalbara beräknings resurser och lagrings platser. Effektiv användning av tillgängliga beräknings resurser genom att köra enskilda pipeline-steg för olika beräknings mål, till exempel HDInsight, GPU: ar för data vetenskap och Databricks.|
|**Återanvändningsmöjligheter**|Skapa pipeline-mallar för vissa scenarier, till exempel omskolning och batch-poäng. Utlös publicerade pipelines från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultat Sök vägar när du itererar, använder du pipelines SDK för att explicit namnge och version av data källor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|
| **Modulariteten** | Genom att avgränsa områden och isolera ändringar kan program vara utvecklas snabbare med högre kvalitet. | 
|**Samarbete**|Pipelines gör det möjligt för data experter att samar beta över alla områden i design processen för maskin inlärning, samtidigt som de kan arbeta med pipeline-steg samtidigt.|

## <a name="next-steps"></a>Nästa steg

Azure ML-pipelines är en kraftfull funktion som börjar leverera värde i de tidiga utvecklings faserna. Värdet ökar när teamet och projektet växer. Den här artikeln innehåller förklaras hur pipeliner anges med Azure Machine Learning python SDK och dirigeras på Azure. Du har sett några grundläggande käll koder och har introducerats för några av de `PipelineStep` klasser som är tillgängliga. Du bör ha en uppfattning om när du ska använda Azure ML-pipeliner och hur Azure kör dem. 


+ Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md).

+ Lär dig hur du [kör batch-förutsägelser på stora data](tutorial-pipeline-batch-scoring-classification.md ).

+ Se SDK-referens dokument för [pipeline-kärnan](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och [pipeline-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Prova Jupyter Notebooks som demonstrerar [Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Lär dig hur du [Kör antecknings böcker för att utforska den här tjänsten](samples-notebooks.md).
