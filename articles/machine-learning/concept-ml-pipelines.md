---
title: Vad är ML Pipelines
titleSuffix: Azure Machine Learning
description: I den här artikeln kan du lära dig fördelarna med de datorinlärningspipelor (ML) som du kan skapa med Azure Machine Learning SDK för Python. Machine learning pipelines används av dataforskare för att bygga, optimera och hantera sina arbetsflöden för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 0cefa78b6f52cc67df8817f68a9b793ab86b2a7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878586"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Vad är Azure Machine Learning-pipelines?

Med Azure Machine Learning-pipelines kan du skapa arbetsflöden i dina maskininlärningsprojekt. Dessa arbetsflöden har ett antal fördelar: 

+ Enkelhet
+ Hastighet
+ Repeterbarhet
+ Flexibilitet
+ Versionshantering och spårning
+ Modularitet 
+ Kvalitetssäkring
+ Kostnadskontroll

Dessa fördelar blir betydande så snart ditt maskininlärningsprojekt går längre än ren utforskning och till iteration. Även enkla ettstegspipelledningar kan vara värdefulla. Machine learning-projekt är ofta i ett komplext tillstånd, och det kan vara en lättnad att göra den exakta genomförandet av ett enda arbetsflöde en trivial process.

Läs om hur du [skapar din första pipeline](how-to-create-your-first-pipeline.md).

![Pipelines för maskininlärning i Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Vilken Azure-pipeline-teknik ska jag använda?

Azure-molnet tillhandahåller flera andra pipelines, var och en med ett annat syfte. I följande tabell visas de olika rörledningarna och vad de används för:

| Scenario | Primär persona | Azure-erbjudande | OSS-erbjudande | Kanoniska rör | Styrkor | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modellorkestrering (Maskininlärning) | Data Scientist | Azure Machine Learning Pipelines | Kubeflow-pipelines | Data -> modell | Distribution, cachelagring, kod först, återanvändning | 
| Dataorkestrering (Data prep) | Datatekniker | [Azure Data Factory-pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache luftflöde | Data -> Data | Starkt skrivna rörelser. Datacentrerade aktiviteter. |
| Kod & fördelningsorkestrering (CI/CD) | Apputvecklare / Ops | [Azure DevOps-pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kod + modell -> App/Tjänst | Mest öppet och flexibelt aktivitetsstöd, godkännandeköer, faser med | 


## <a name="what-can-azure-ml-pipelines-do"></a>Vad kan Azure ML-pipelines göra?

En Azure Machine Learning-pipeline är ett oberoende körbart arbetsflöde för en fullständig maskininlärningsuppgift. Underaktiviteter kapslas in som en serie steg i pipelinen. En Azure Machine Learning-pipeline kan vara så enkel som en som anropar ett Python-skript, så _kan_ göra nästan vad som helst. Rörledningar _bör_ fokusera på maskininlärningsuppgifter som:

+ Förberedelse av data, inklusive import, validering och rensning, omvandling och transformering, normalisering och mellanlagring
+ Träningskonfiguration, inklusive parametrisera argument, sökvägar och loggnings-/rapporteringskonfigurationer
+ Utbildning och validering effektivt och upprepade gånger. Effektivitet kan komma från att ange specifika dataundermängder, olika maskinvaruberäkningsresurser, distribuerad bearbetning och förloppsövervakning
+ Distribution, inklusive versionshantering, skalning, etablering och åtkomstkontroll 

Oberoende steg gör det möjligt för flera dataforskare att arbeta på samma pipeline samtidigt utan att överbeskatta beräkningsresurser. Separata steg gör det också enkelt att använda olika beräkningstyper/storlekar för varje steg.

När rörledningen är utformad, finns det ofta mer finjustering runt utbildningen slingan av rörledningen. När du kör en pipeline igen hoppar körningen till de steg som måste köras igen, till exempel ett uppdaterat utbildningsskript. Steg som inte behöver köras igen hoppas över. Samma analys gäller oförändrade skript som används för att utföra steget. Den här återanvändningsfunktionen hjälper till att undvika att köra kostsamma och tidskrävande steg som datainmatning och omvandling om de underliggande data inte har ändrats.

Med Azure Machine Learning kan du använda olika verktygslådor och ramverk, till exempel PyTorch eller TensorFlow, för varje steg i pipelinen. Azure samordnar de olika [beräkningsmål](concept-azure-machine-learning-architecture.md) du använder, så att dina mellanliggande data kan delas med beräkningsmålen nedströms.

Du kan [spåra måtten för dina pipelineexperiment](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) direkt i Azure Portal eller din [målsida för arbetsytan (förhandsgranskning)](https://ml.azure.com). När en pipeline har publicerats kan du konfigurera en REST-slutpunkt, som gör att du kan köra pipelinen från valfri plattform eller stack.

Kort sagt, alla komplexa uppgifter i maskininlärningslivscykeln kan få hjälp med pipelines. Andra Azure pipeline-tekniker har sina egna styrkor. [Azure Data Factory-pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) utmärker sig när du arbetar med data och [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) är rätt verktyg för kontinuerlig integrering och distribution. Men om ditt fokus är maskininlärning är Azure Machine Learning-pipelines sannolikt det bästa valet för dina arbetsflödesbehov. 

## <a name="what-are-azure-ml-pipelines"></a>Vad är Azure ML-pipelines?

En Azure ML-pipeline utför ett fullständigt logiskt arbetsflöde med en ordnad sekvens av steg. Varje steg är en diskret bearbetningsåtgärd. Pipelines körs i samband med ett Azure Machine Learning [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

I de tidiga stadierna av ett ML-projekt är det bra att ha en enda Jupyter-anteckningsbok eller Python-skript som gör allt arbete med Azure-arbetsyta och resurskonfiguration, dataförberedelser, kör konfiguration, utbildning och validering. Men precis som funktioner och klasser snabbt blir att föredra framför en enda tvingande block av kod, ML arbetsflöden blir snabbt att föredra framför en monolitisk anteckningsbok eller skript. 

Genom modularisering ML uppgifter, rörledningar stödja datavetenskap imperativ att en komponent bör "göra (bara) en sak väl." Modularitet är helt klart avgörande för att projektet framgång när programmering i team, men även när man arbetar ensam, även ett litet ML-projekt innebär separata uppgifter, var och en med en god mängd komplexitet. Uppgifter inkluderar: arbetsytekonfiguration och dataåtkomst, dataförberedelse, modelldefinition och konfiguration samt distribution. Medan utdata för en eller flera uppgifter utgör indata till en annan, är de exakta implementeringsdetaljerna för en uppgift i bästa fall irrelevanta distraktioner i nästa. I värsta fall kan beräkningstillståndet för en aktivitet orsaka en bugg i en annan. 

### <a name="analyzing-dependencies"></a>Analysera beroenden

Många programmeringsekosystem har verktyg som dirigerar resurs-, biblioteks- eller kompileringsberoenden. I allmänhet använder dessa verktyg filtidsstämplar för att beräkna beroenden. När en fil ändras uppdateras bara den och dess anhöriga (hämtas, kompileras om eller paketeras). Azure ML-pipelines utökar det här konceptet dramatiskt. Precis som traditionella byggverktyg beräknar pipelines beroenden mellan steg och utför bara nödvändiga omberäkningar. 

Beroendeanalysen i Azure ML-pipelines är mer sofistikerad än enkla tidsstämplar dock. Varje steg kan köras i en annan maskin- och programvarumiljö. Dataförberedelse kan vara en tidskrävande process men behöver inte köras på maskinvara med kraftfulla GPU:er, vissa steg kan kräva OS-specifik programvara, du kanske vill använda distribuerad utbildning och så vidare. Medan kostnadsbesparingarna för att optimera resurser kan vara betydande, kan det vara överväldigande att manuellt jonglera alla olika varianter i hårdvara och mjukvara resurser. Det är ännu svårare att göra allt detta utan att någonsin göra ett misstag i de data du överför mellan steg. 

Pipelines lösa detta problem. Azure Machine Learning dirigerar automatiskt alla beroenden mellan pipeline-steg. Den här orkestreringen kan omfatta att snurra upp och ned Docker-avbildningar, koppla och koppla från beräkningsresurser och flytta data mellan stegen på ett konsekvent och automatiskt sätt.

### <a name="reusing-results"></a>Återanvända resultat

Dessutom kan utdata från ett steg, om du väljer det, återanvändas. Om du anger återanvändning som en möjlighet och det inte finns några uppströmsberoenden som utlöser omberäkning, använder pipeline-tjänsten en cachelagrad version av stegets resultat. Sådan återanvändning kan dramatiskt minska utvecklingstiden. Om du har en komplex uppgift för förberedelse av data kör du den förmodligen oftare än vad som är absolut nödvändigt. Pipelines befria dig från denna oro: om det behövs kommer steget att köras, om inte, kommer det inte.

All den här beroendeanalysen, orkestreringen och aktiveringen hanteras av Azure Machine `Experiment`Learning `submit()`när du instansierar ett [Pipeline-objekt,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) skickar det till ett och anropar . 

### <a name="coordinating-the-steps-involved"></a>Samordning av de berörda stegen

När du skapar `Pipeline` och kör ett objekt inträffar följande steg på hög nivå:

+ För varje steg beräknar tjänsten kraven för:
    + Beräkningsresurser för maskinvara
    + OS-resurser (Docker-avbildningar)
    + Programvaruresurser (Conda / virtualenv beroenden)
    + Indata 
+ Tjänsten bestämmer beroenden mellan steg, vilket resulterar i ett dynamiskt körningsdiagram
+ När varje nod i körningsdiagrammet körs:
    + Tjänsten konfigurerar den nödvändiga maskinvaru- och programvarumiljön (kanske återanvända befintliga resurser)
    + Steget körs, vilket ger loggning och `Experiment` övervakningsinformation till dess innehållande objekt
    + När steget är klart förbereds dess utgångar som ingångar till nästa steg och/eller skrivs till
    + Resurser som inte längre behövs slutförs och tas bort

![Steg för pipeline](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Bygga rörledningar med Python SDK

I [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)är en pipeline `azureml.pipeline.core` ett Python-objekt som definierats i modulen. Ett [Pipeline-objekt](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) innehåller en ordnad sekvens med ett eller flera [PipelineStep-objekt.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) Klassen `PipelineStep` är abstrakt och de faktiska stegen kommer att vara av underklasser som [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)eller [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). Klassen [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) innehåller en återanvändbar sekvens av steg som kan delas mellan pipelines. A `Pipeline` körs som `Experiment`en del av en .

En Azure ML-pipeline är associerad med en Azure Machine Learning-arbetsyta och ett pipeline-steg är associerat med ett beräkningsmål som är tillgängligt inom den arbetsytan. Mer information finns [i Skapa och hantera Azure Machine Learning-arbetsytor i Azure-portalen](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) eller Vad är [beräkningsmål i Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).

I Azure Machine Learning är ett beräkningsmål den miljö där en ML-fas inträffar. Programvarumiljön kan vara en fjärr-VM, Azure Machine Learning Compute, Azure Databricks, Azure Batch och så vidare. Maskinvarumiljön kan också variera kraftigt, beroende på GPU-stöd, minne, lagring och så vidare. Du kan ange beräkningsmålet för varje steg, vilket ger dig finkornig kontroll över kostnaderna. Du kan använda mer eller mindre kraftfulla resurser för projektets specifika åtgärd, datavolym och prestandabehov. 

## <a name="building-pipelines-with-the-designer"></a>Bygga rörledningar med designern

Utvecklare som föredrar en visuell designyta kan använda Azure Machine Learning-designern för att skapa pipelines. Du kan komma åt det här verktyget från **designervalet** på arbetsytans startsida.  Med designern kan du dra och släppa steg på designytan. För snabb utveckling kan du använda befintliga moduler över hela spektrumet av ML-uppgifter. befintliga moduler täcker allt från dataomvandling till algoritmval till utbildning till distribution. Du kan också skapa en helt anpassad pipeline genom att kombinera dina egna steg som definierats i Python-skript.

När du visuellt utformar pipelines visas indata och utdata för ett steg synligt. Du kan dra och släppa dataanslutningar, så att du snabbt kan förstå och ändra dataflödet för din pipeline.
 
![Exempel på Azure Machine Learning-designer](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Förstå körningsdiagrammet

Stegen i en pipeline kan ha beroenden på andra steg. Azure ML-pipelinetjänsten gör arbetet med att analysera och iscensätta dessa beroenden. Noderna i den resulterande "körningsdiagrammet" bearbetar steg. Varje steg kan innebära att skapa eller återanvända en viss kombination av maskinvara och programvara, återanvända cachelagrade resultat och så vidare. Tjänstens orkestrering och optimering av denna körning graf kan avsevärt påskynda en ML fas och minska kostnaderna. 

Eftersom stegen körs oberoende av varandra måste objekt för att lagra in- och utdata som flödar mellan steg definieras externt. Detta är rollen för [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)och [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), objekt. Dessa dataobjekt är associerade med ett [Datastore-objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) som kapslar in deras lagringskonfiguration. Basklassen `PipelineStep` skapas alltid `name` med en `inputs`sträng, en `outputs`lista över och en lista över . Vanligtvis har det också `arguments` en lista över och `resource_inputs`ofta kommer det att ha en lista över . Underklasser har i allmänhet också ytterligare `PythonScriptStep` argument (kräver till exempel filnamn och sökväg för skriptet för att köras). 

Körningsdiagrammet är acykliskt, men pipelines kan köras enligt ett återkommande schema och kan köra Python-skript som kan skriva tillståndsinformation till filsystemet, vilket gör det möjligt att skapa komplexa profiler. Om du utformar pipelinen så att vissa steg kan köras parallellt eller asynkront hanterar Azure Machine Learning transparent beroendeanalys och samordning av fan-out och fan-in. Du behöver i allmänhet inte bry dig om detaljerna i körningsdiagrammet, men det är tillgängligt via attributet [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>En enkel Python Pipeline

Det här kodavsnittet visar de objekt och `Pipeline`anrop som behövs för att skapa och köra ett grundläggande:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Kodavsnittet börjar med vanliga Azure Machine `Workspace`Learning-objekt, en , en `Datastore`, en [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)och en `Experiment`. Sedan skapar koden objekten att `input_data` `output_data`hålla och . Matrisen `steps` innehåller ett enda `PythonScriptStep` element, a som använder `compute_target`dataobjekten och körs på . Sedan instansierar koden `Pipeline` själva objektet och passerar i arbetsytan och stegmatrisen. Anropet `experiment.submit(pipeline)` för att påbörja Azure ML-pipelinekörningen. Anropet `wait_for_completion()` tills pipelinen är klar. 

Mer information om hur du ansluter pipelinen till dina data finns i artiklarna [Dataåtkomst i Azure Machine Learning](concept-data.md) och Flytta data till och mellan ML pipeline steps [(Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="best-practices-when-using-pipelines"></a>Metodtips när du använder pipelines

Som du kan se är det lite mer komplicerat att skapa en Azure ML-pipeline än att starta ett skript. Pipelines kräver att några Python-objekt konfigureras och skapas. 

Vissa situationer som tyder på att använda en pipeline:

* I en gruppmiljö: Dela in ML-uppgifter i flera oberoende steg så att utvecklare kan arbeta och utveckla sina program självständigt. 

* När du är i eller i närheten av distribution: spika fast konfigurationen och använd schemalagda och händelsedrivna åtgärder för att hålla koll på att ändra data.

* I ett tidigt skede av ett ML-projekt eller arbetar ensam: använd pipelines för att automatisera byggnaden. Om du har börjat oroa dig för att återskapa konfigurations- och beräkningstillståndet innan du implementerar en ny idé, är det en signal som du kan överväga att använda en pipeline för att automatisera arbetsflödet. 

Det är lätt att bli entusiastisk över att återanvända cachelagrade resultat, finkornig kontroll över beräkningskostnader och processisolering, men pipelines har kostnader. Några anti-mönster inkluderar:

* Använda rörledningar som det enda sättet att skilja problem. Pythons inbyggda funktioner, objekt och moduler räcker långt för att undvika förvirrande programmatiskt tillstånd! Ett pipeline-steg är mycket dyrare än ett funktionsanrop.

* Tung koppling mellan rörledningssteg. Om det ofta krävs att ett beroende steg ändras i ett tidigare steg, är det troligt att separata steg för närvarande är mer av en kostnad än en förmån. En annan ledtråd som stegen är alltför kopplade är argument till ett steg som inte är data utan flaggor för att styra bearbetningen. 

* Optimera beräkningsresurser i förtid. Till exempel finns det ofta flera steg till dataförberedelse och man kan ofta `MpiStep` se "Åh, här är en plats `PythonScriptStep` där jag kunde använda en för parallell-programmering men här är en plats där jag kunde använda en med en mindre kraftfull beräkning mål", och så vidare. Och kanske, i det långa loppet, skapa finkorniga steg som det kan visa sig värt, särskilt om det finns en möjlighet att använda cachade resultat snarare än att alltid räkna om. Men pipelines är inte avsedda att ersätta Pythons inbyggda `multiprocessing` modul. 

Tills ett projekt blir stort eller närmar sig distribution, bör dina pipelines vara grådare snarare än finkornig. Om du ser ml-projektet som involverande _faser_ och en pipeline som ger ett komplett arbetsflöde för att flytta dig genom ett visst skede, är du på rätt väg. 

## <a name="key-advantages"></a>Viktiga fördelar

De viktigaste fördelarna med att använda pipelines för dina maskininlärningsarbetsflöden är:

|Viktig fördel|Beskrivning|
|:-------:|-----------|
|**Obevakade&nbsp;körningar**|Schemalägg steg så att de körs parallellt eller i följd på ett tillförlitligt och obevakat sätt. Dataförberedelse och modellering kan pågå i dagar eller veckor, och pipelines kan du fokusera på andra uppgifter medan processen körs. |
|**Heterogen beräkning**|Använd flera pipelines som är tillförlitligt koordinerade över heterogena och skalbara beräkningsresurser och lagringsplatser. Använd tillgängliga beräkningsresurser effektivt genom att köra enskilda pipeline-steg på olika beräkningsmål, till exempel virtuella hårddiskar, virtuella GPU-datavetenskap och Databricks.|
|**Återanvändning**|Skapa pipelinemallar för specifika scenarier, till exempel omskolning och batchbedömning. Utlösa publicerade pipelines från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultatsökvägar när du itererar använder du pipelines SDK för att uttryckligen namnge och version av dina datakällor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|
| **Modularitet** | Separera områden av oro och isolera förändringar gör att programvaran kan utvecklas i snabbare takt med högre kvalitet. | 
|**Samarbete**|Pipelines gör det möjligt för datavetare att samarbeta inom alla områden i maskininlärningsdesignprocessen, samtidigt som de samtidigt kan arbeta med pipeline-steg.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Välja rätt PipelineStep-underklass

Den `PythonScriptStep` är den mest flexibla `PipelineStep`underklassen i det abstrakta . Andra underklasser, `EstimatorStep` till exempel `DataTransferStep` underklasser och kan utföra specifika uppgifter med mindre kod. Till exempel `EstimatorStep` kan en skapas bara genom att skicka `Estimator`in ett namn för steget, ett och ett beräkningsmål. Du kan också åsidosätta indata och utdata, pipelineparametrar och argument. Mer information finns i [Träna modeller med Azure Machine Learning med uppskattning .](how-to-train-ml-models.md) 

Det `DataTransferStep` gör det enkelt att flytta data mellan datakällor och sänkor. Koden för att göra denna överföring manuellt är enkel men repetitiv. I stället kan du `DataTransferStep` bara skapa en med ett namn, referenser till en datakälla och en datamottagare och ett beräkningsmål. Den bärbara [Azure Machine Learning Pipeline med DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) visar den här flexibiliteten.

## <a name="modules"></a>Moduler

Medan pipeline-steg tillåter återanvändning av resultaten av en tidigare körning, förutsätter i många fall konstruktionen av steget att skript och beroende filer som krävs måste vara lokalt tillgängliga. Om en datavetare vill bygga ovanpå befintlig kod måste skripten och beroendena ofta klonas från en separat databas.

Moduler är liknande i användning till pipeline steg, men ger versionshantering underlättas genom arbetsytan, vilket möjliggör samarbete och återanvändning i stor skala. Moduler är utformade för att återanvändas i flera pipelines och kan utvecklas för att anpassa en specifik beräkning till olika användningsfall. Användare kan utföra följande uppgifter via arbetsytan utan att använda externa databaser:

* Skapa nya moduler och publicera nya versioner av befintliga moduler
* Föra bort befintliga versioner
* Markera versioner som inaktiverats för att hindra konsumenter från att använda den versionen
* Ange standardversioner
* Hämta moduler efter version från arbetsytan för att säkerställa att teamen använder samma kod

Se [de anteckningsboks-exemplen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) på kodexempel på hur du skapar, ansluter och använder moduler i Azure Machine Learning-pipelines.

## <a name="next-steps"></a>Nästa steg

Azure ML-pipelines är en kraftfull anläggning som börjar leverera värde i de tidiga utvecklingsstadierna. Värdet ökar i takt med att teamet och projektet växer. Den här artikeln har förklarat hur pipelines anges med Azure Machine Learning Python SDK och orkestreras på Azure. Du har sett några grundläggande källkod och införts `PipelineStep` för att några av de klasser som är tillgängliga. Du bör ha en känsla för när du ska använda Azure ML-pipelines och hur Azure kör dem. 


+ Läs om hur du [skapar din första pipeline](how-to-create-your-first-pipeline.md).

+ Lär dig hur du [kör batchprediktioner på stora data](tutorial-pipeline-batch-scoring-classification.md ).

+ Se SDK-referensdokumenten för [pipeline-kärn-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och [pipelinesteg](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Prova exempel på Jupyter-anteckningsböcker som visar [Azure Machine Learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Lär dig hur du [kör anteckningsböcker för att utforska den här tjänsten](samples-notebooks.md).
