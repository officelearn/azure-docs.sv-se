---
title: Vad är nytt i version?
titleSuffix: Azure Machine Learning service
description: 'Läs om de senaste uppdateringarna för Azure Machine Learning-tjänsten och den machine learning och data Förbered Python SDK: er.'
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742770"
---
# <a name="azure-machine-learning-service-release-notes"></a>Viktig information för Azure Machine Learning-tjänsten

I den här artikeln lär du dig om Azure Machine Learning-tjänstversioner. 

## <a name="2018-12-20"></a>2018-12-20: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning-SDK för Python v1.0.6

+ **Referensdokument för SDK**: https://aka.ms/aml-sdk

+ **Felkorrigeringar**: Den här versionen innehåller främst mindre felkorrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Förbered SDK v1.0.4

+ **Referensdokument för SDK**: https://aka.ms/data-prep-sdk

+ **Nya funktioner**
  + `to_bool` funktionen kan nu Felmatchade värden som ska konverteras till felvärden. Det här är den nya matchningsfel för standardfunktionen för `to_bool` och `set_column_types`, medan tidigare standardbeteendet var att konvertera Felmatchade värden till False.
  + När du anropar `to_pandas_dataframe`, det finns en ny möjlighet att tolka null/saknat värdena i numeriska kolumner som NaN.
  + Möjlighet att kontrollera Returtypen för vissa uttryck för att säkerställa konsekvens typ och misslyckas tidigt har lagts till.
  + Nu kan du anropa `parse_json` att parsa värdena i en kolumn som JSON-objekt och expandera dem i flera kolumner.

+ **Felkorrigeringar**
  + En bugg kraschat `set_column_types` i Python 3.5.2.
  + Åtgärdat en bugg kraschat när du ansluter till datalagret med hjälp av en AML-avbildning.

+ **Uppdateringar**
  * [Exempel anteckningsböcker](https://aka.ms/aml-data-prep-notebooks) för att komma igång-självstudier, fallstudier och instruktionsguider.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Allmän tillgänglighet

Azure Machine Learning-tjänsten är nu allmänt tillgänglig.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
Med den här versionen har Vi presenterar en ny hanterad beräkningsmiljö via den [beräkning av Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Den här beräkningsmål ersätter Azure Batch AI-databearbetning för Azure Machine Learning. 

Den här beräkningsmål:
+ Används för modellen utbildnings- och batch inferensjobb
+ Är en - till Multi-Factor - node beräkning
+ Utför klusterhanteringen och jobbschemaläggning för användaren
+ Skalar som standard
+ Stöd för både CPU- och GPU-resurser 
+ Möjliggör användning av lågprioriterade virtuella datorer för lägre kostnad

Beräkning av Azure Machine Learning kan skapas i Python, med hjälp av Azure-portalen eller CLI. Den måste skapas i regionen för din arbetsyta och går inte att ansluta till en annan arbetsyta. Den här beräkningsmål använder en Docker-behållare för din körning och paketerar dina beroenden för att replikera samma miljö på alla noder.

> [!Warning]
> Vi rekommenderar att du skapar en ny arbetsyta om du vill använda beräkning av Azure Machine Learning. Det finns en fjärransluten risk att användare som försöker skapa beräkning av Azure Machine Learning från en befintlig arbetsyta kan se ett fel. Befintliga beräkning i din arbetsyta bör fortsätta att fungera påverkas inte.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning-SDK för Python v1.0.2
+ **Större ändringar**
  + Den här versionen har bort vi stöd för att skapa en virtuell dator från Azure Machine Learning. Du kan fortfarande koppla ett befintligt moln virtuell dator eller en fjärransluten lokal server. 
  + Vi också tar bort sitt stöd för BatchAI, som ska ha stöd via Azure Machine Learning Compute nu.

+ **Ny**
  + För machine learning pipelines:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **uppdaterad**
  + För machine learning pipelines:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) accepterar nu runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) nu kopieras till och från en SQL-datakälla
    + Schemalägga funktioner i SDK för att skapa och uppdatera scheman för att köra publicerade pipelines

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Förbered SDK v0.5.2
+ **Större ändringar** 
  * `SummaryFunction.N` har bytt namn till `SummaryFunction.Count`.
  
+ **Felkorrigeringar**
  * Använd senaste AML kör Token vid läsning från och skriva till datalager på fjärranslutna körs. Tidigare, om den AML kör Token uppdateras i Python, Data Prep-runtime kommer inte att uppdateras med den uppdaterade AML kör Token.
  * Ytterligare tydligare felmeddelanden
  * to_spark_dataframe() kommer inte längre krascha när de använder Spark `Kryo` serialisering
  * Antal värden Inspector kan nu visa fler än 1000 unika värden
  * Slumpmässig dela misslyckas inte längre om det ursprungliga dataflödet saknar ett namn  

+ **Mer information**
  * [Azure Machine Learning-SDK för dataförberedelse](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs och bärbara datorer
+ ML-Pipelines
  + Nya och uppdaterade anteckningsböcker för att komma igång med pipelines, batch omfång och format överföra exempel: https://aka.ms/aml-pipeline-notebooks
  + Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md)
  + Lär dig hur du [kör batch förutsägelser med pipelines](how-to-run-batch-predictions.md)
+ Azure Machine Learning-beräkningsmål
  + [Exempelanteckningsböcker] (https://aka.ms/aml-notebooks) uppdateras nu om du vill använda den nya hantera databearbetning.
  + [Lär dig mer om den här beräkning](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-portalen: nya funktioner
+ Skapa och hantera [beräkning av Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) typer i portalen.
+ Övervaka kvotanvändning och [begär kvot](how-to-manage-quotas.md) för beräkning av Azure Machine Learning.
+ Visa status för beräkning av Azure Machine Learning-kluster i realtid.
+ Virtual network-stöd har lagts till för att skapa en beräkning av Azure Machine Learning och Azure Kubernetes Service.
+ Kör dina publicerade pipelines med befintliga parametrar.
+ Ny [automatiserad machine learning diagram](how-to-track-experiments.md#auto) för klassificering modeller (lift, vinster, kalibrering, funktionen vikten diagram med modellen explainability) och regressionsmodeller (restbelopp och vikten funktionsdiagram med modellen explainability). 
+ Pipelines kan ses i Azure-portalen




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning-SDK för Python v0.1.80

+ **Större ändringar** 
  * *azureml.Train.widgets* namnområde har flyttats till *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* gör blir föråldrat följande klasser - *azureml.core.compute.BatchAICompute* och *azureml.core.compute.DSVMCompute*. Klassen senare tas bort i kommande versioner. Klassen AmlCompute har nu en enklare definition bara behöver en vm_size och max_nodes och skalar automatiskt ditt kluster från 0 till max_nodes när ett jobb skickas. Vår [exempelanteckningsböcker] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) har uppdaterats med den här informationen och bör ge dig användningsexempel. Vi hoppas att du som den här förenkling av distribution och mycket mer spännande funktioner kommer i en senare version!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Förbered SDK v0.5.1 

Läs mer om Data Prep SDK genom att läsa [referera till docs](https://aka.ms/data-prep-sdk).
+ **Nya funktioner**
   * Skapa en ny DataPrep CLI för att köra paket för förberedelse av data och visa data för en datauppsättning eller dataflöde
   * Omarbetad SetColumnType API för att förbättra användbarhet
   * Omdöpt smart_read_file till auto_read_file
   * Innehåller nu skeva och datamängds i profilen för Data
   * Kan ta prov med stratified sampling
   * Kan läsa från zip-filer som innehåller CSV-filer
   * Dela upp datauppsättningar efter rader med slumpmässiga Split (till exempel till test-train-uppsättningar)
   * Kan hämta kolumnen datatyper från en dataflöde eller en dataprofil för genom att anropa `.dtypes`
   * Kan få antalet rader som ett dataflöde eller en dataprofil för genom att anropa `.row_count`

+ **Felkorrigeringar**
   * Fast länge till dubbla konvertering 
   * Fast assert när någon Lägg till kolumn 
   * Ett problem har åtgärdats med FuzzyGrouping, där den inte kan identifiera grupper i vissa fall
   * Fast sorteringsfunktionen respekterar sorteringsordning för flera kolumner
   * Fast och/eller uttryck liknar hur `pandas` hanterar dem
   * Fast läsning från antingen path
   * Gjort det lättare att förstå felmeddelanden 
   * Nu inte längre misslyckas vid läsning på fjärranslutna beräkningsmål med AML-token
   * Nu inte längre på Linux DSVM
   * Nu kraschar inte längre när icke-sträng värden är i strängen predikat
   * Hanterar nu assertion fel när dataflöde inte bör fungera korrekt
   * Stöder nu dbutils monterad lagringsplatser på Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal 
Azure-portalen för Azure Machine Learning-tjänsten har följande uppdateringar:
  * En ny **Pipelines** fliken för publicerade pipelines.
  * Stöd har lagts till för att bifoga ett befintligt HDInsight-kluster som en beräkningsmål.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning-SDK för Python v0.1.74

+ **Större ändringar** 
  * * Workspace.compute_targets, datalager, experiment, bilder, modeller och *webservices* egenskaper i stället för metoder. Ersätt till exempel *Workspace.compute_targets()* med *Workspace.compute_targets*.
  * *Run.get_context* gör blir föråldrat *Run.get_submitted_run*. Den andra metoden tas bort i kommande versioner.
  * *PipelineData* klass nu förväntar sig ett datalager-objekt som en parameter i stället för datastore_name. På samma sätt *Pipeline* accepterar default_datastore snarare än default_datastore_name.

+ **Nya funktioner**
  * Azure Machine Learning Pipelines [exempel notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) använder nu MPI steg.
  * Widgeten RunDetails för Jupyter-anteckningsböcker uppdateras för att visa en visualisering av pipelinen.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Förbered SDK v0.4.0 
 
+ **Nya funktioner**
  * Antal läggs till Dataprofil 
  * Antal värden och Histogram är nu tillgänglig
  * Mer percentiler i Dataprofil
  * Medianvärdet är tillgänglig i sammanfatta
  * Python 3.7 stöds nu
  * När du sparar ett dataflöde med datalager i ett paket för förberedelse av data, ska datastore-information sparas som en del av paketet för förberedelse av data
  * Nu finns stöd för skrivning till datalagret 
        
+ **Bugg har åtgärdats**
  * 64-bitars heltal utan tecken egenskapslängd hanteras nu korrekt på Linux
  * Fast felaktig textetikett för filer med oformaterad text i smart_read
  * Sträng kolumntyp nu visas i måttvyn
  * Antal löses nu att visa ValueKinds som mappats till enkel FieldType i stället för enskilda finns
  * Write_to_csv misslyckas inte längre när sökväg har angetts som en sträng
  * När du använder Ersätt misslyckas lämnar ”hitta” tom inte längre 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning-SDK för Python v0.1.68

+ **Nya funktioner**
  * Stöd för flera klienter när du skapar en ny arbetsyta.

+ **Korrigerade buggar**
  * Du behöver inte längre att fästa pynacl library version när du distribuerar webbtjänsten.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Förbered SDK v0.3.0

+ **Nya funktioner**
  * Lagt till metoden transform_partition_with_file(script_path), vilket gör att användare att skicka in sökvägen till en Python-fil för att köra

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning-SDK för Python v0.1.65
[Version 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) innehåller nya funktioner, mer dokumentation, felkorrigeringar och mer [exempel anteckningsböcker](https://aka.ms/aml-notebooks).

Se [lista över kända problem](resource-known-issues.md) att lära dig om kända fel och lösningar.

+ **Större ändringar**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images Workspace.web_services returnerade ordlista, som tidigare returnerats lista. Se [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-dokumentationen.

  * Automatiserade Machine Learning bort normaliserade medelvärdet kvadrat fel från den primära måtten.

+ **HyperDrive**
  * Olika HyperDrive felkorrigeringar för Bayesian, prestandaförbättringar för hämta mått-anrop. 
  * Tensorflow 1.10 uppgradering från 1.9 
  * Docker-avbildning optimering för kallstart. 
  * Jobb rapportera nu rätt status, även om de avslutas med felkod än 0. 
  * RunConfig attributet validering i SDK. 
  * HyperDrive kör objektet stöder Avbryt liknar en vanlig körning: du behöver inte vidarebefordra alla parametrar. 
  * Widget förbättringar för att underhålla tillståndet för listrutan värden för distribuerade körningar och HyperDrive körs. 
  * TensorBoard och andra loggar som stöd för filer som fasta för parametern-server. 
  * Intel(R) MPI stöd på serversidan. 
  * Bugfix till parametern justering för distribuerade kör korrigering vid verifiering av i BatchAI. 
  * Kontext Manager identifierar nu den primära instansen. 

+ **Azure-portalen**
  * log_table() och log_row() stöds i informationen om körningen. 
  * Automatiskt skapa diagram för tabeller och rader med 1, 2 eller 3 numeriska kolumner och en valfri kategoriska kolumn.

+ **Automatiserad Machine Learning**
  * Förbättrad felhantering och dokumentation 
  * Fasta kör egenskapshämtning prestandaproblem. 
  * Fast fortsätta köra problemet. 
  * Ensembling iteration ett problem har åtgärdats.
  * Fast utbildning hängande bugg i MAC OS.
  * Nedsampling makrot genomsnittlig pull-begäran/ROC-kurvan i scenario med anpassad validering.
  * Ta bort extra index logik.
  * Ta bort filter från get_output API.

+ **Pipelines**
  * Lägga till en metod Pipeline.publish() att publicera en pipeline direkt, utan att kräva en körning köras först.   
  * Lägga till en metod PipelineRun.get_pipeline_runs() att hämta pipeline-körningar som har genererats från en publicerade pipeline.

+ **Project Brainwave**
  * Uppdaterade stöd för nya AI-modeller som finns på FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Förbered SDK v0.2.0
[Version 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) innehåller följande funktioner och felkorrigeringar:

+ **Nya funktioner**
  * Stöd för en frekvent kodning
  * Stöd för quantile transformering
   
+ **Bugg har åtgärdats:**
  * Fungerar med en storm-version du behöver inte nedgradera din storm-version
  * Antal värden för alla värden, inte bara de tre överst

## <a name="2018-09-public-preview-refresh"></a>2018-09 (offentlig förhandsversion uppdatera)

En ny uppdaterad version av Azure Machine Learning: Läs mer om den här versionen: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Äldre kommentarer: September 2017 – Jun 2018
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

Med den här versionen av Azure Machine Learning kan du:
+ Funktionalisera bilder med en quantized version av ResNet-50, träna en klassificerare som baseras på dessa funktioner och [distribuerar den modellen till en FPGA på Azure](../service/how-to-deploy-fpga-web-service.md) för extremt låg latens inferensjobb.

+ Snabbt bygga och driftsätta mycket exakta machine learning och modeller för djupinlärning med [anpassade Azure Machine Learning-paket](../desktop-workbench/reference-python-package-overview.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Versionsnumret**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Många av följande uppdateringar görs som direkt resultat av din feedback. Nyårslöfte!

**Viktiga nya funktioner och ändringar**

- Stöd för att köra dina skript på fjärranslutna virtuella Ubuntu-datorer internt på din egen miljö förutom fjärransluten docker-baserad körning.
- Den nya miljön upplevelsen i Workbench-appen kan du skapa beräkningsmål och kör konfigurationer förutom vår CLI-baserade erfarenhet.
![Fliken miljöer](media/azure-machine-learning-release-notes/environment-page.png)
- Anpassningsbara Körningshistorik rapporterar ![bild av ny köra rapporter om Enhetshistorik](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Detaljerad uppdateringar**

Följande är en lista med detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

#### <a name="workbench-ui"></a>Workbench UI
- Anpassningsbara rapporter med Körningshistorik
  - Förbättrad diagramkonfigurationen för Körningshistorik rapporter
    - Använda startpunkterna kan ändras
    - Översta filter kan läggas till och ändras ![Lägg till filter](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Diagram och statistik kan läggs till eller ändras (och dra och släpp om).
    ![Skapa nytt diagram](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD för Körningshistorik rapporter
  - Flytta alla befintliga kör historik listvyn konfigurationsfilerna till rapporten på serversidan, som fungerar som pipelines på körs från de valda startpunkterna.

- Fliken miljöer
  - Enkelt lägga till nya och köra konfigurationsfiler i projektet ![mål för nya Compute](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Hantera och uppdatera konfigurationsfilerna med hjälp av en enkel, formulärbaserad UX
  - Knapp för ny för att förbereda dina miljöer för körning

- Prestandaförbättringar i listan över filer i sidopanelen

#### <a name="data-preparation"></a>Förberedelse av data 
- Azure Machine Learning Workbench kan du nu för att kunna söka efter en kolumn med en känd kolumnnamn.


#### <a name="experimentation"></a>Experimentering
- Azure Machine Learning Workbench stöder nu köra dina skript internt på din egen python eller pyspark-miljö. För den här funktionen användaren skapar och hanterar sina egna miljö på Virtuellt fjärrdatorn, och använda Azure Machine Learning Workbench för att köra sina skript på målet. Se [konfigurerar Azure Machine Learning-experimentering](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Modellhantering
- Stöd för att anpassa de behållare som distribueras: gör det möjligt att anpassa behållaravbildningen genom att tillåta installation av externa bibliotek med apt-get etc. Det är inte längre begränsad till pip-installation-bibliotek. Se den [dokumentation](../desktop-workbench/model-management-custom-container.md) för mer information.
  - Använd den `--docker-file myDockerStepsFilename` flaggan och filnamnet med manifest, bild eller tjänst skapas kommandon.
  - Basavbildningen är Ubuntu och kan inte ändras.
  - Exempel-kommando: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Versionsnumret**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Här följer uppdateringar och förbättringar i den här sprint. Många av de här uppdateringarna görs direkt följd av feedback från användare. 

### <a name="2017-12-sprint-2"></a>2017-12 (sprint 2)
**Versionsnumret**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Den här versionen är den tredje uppdateringen av Azure Machine Learning. Den här uppdateringen innehåller förbättringar i workbench-appen och kommandoradsgränssnittet (CLI) backend-tjänster. Tack så mycket för att skicka leenden och frowns. Många av följande uppdateringar görs som direkt resultat av din feedback. 

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Versionsnumret**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vi har gjort förbättringar kring säkerhet, stabilitet och underhåll i workbench-appen och CLI lagret backend-tjänster i den här versionen. 

### <a name="2017-10-sprint-0"></a>2017-10 (sprint 0) 
**Versionsnumret**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Den här versionen är den första uppdateringen av Azure Machine Learning Workbench följa vår första offentlig förhandsversion på Microsoft Ignite 2017-konferensen. De viktigaste uppdateringarna i den här versionen är tillförlitlighet och stabiliserade korrigeringar. 

## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
