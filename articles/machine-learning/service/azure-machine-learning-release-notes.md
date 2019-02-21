---
title: Vad är nytt i version?
titleSuffix: Azure Machine Learning service
description: 'Läs om de senaste uppdateringarna för Azure Machine Learning-tjänsten och den machine learning och data Förbered Python SDK: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: f02a44f41eba8cc4298b9fc730354799ca0aad0c
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446774"
---
# <a name="azure-machine-learning-service-release-notes"></a>Viktig information för Azure Machine Learning-tjänsten

I den här artikeln lär du dig om Azure Machine Learning-tjänstversioner.  En fullständig beskrivning av varje SDK finns i referensdokument för:
+ Azure Machine Learning [ **huvudsakliga SDK för Python**](https://aka.ms/aml-sdk)
+ Azure Machine Learning [ **Data Förbered SDK**](https://aka.ms/data-prep-sdk)

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning-SDK för Python v1.0.15

+ **Nya funktioner**
  + Azure Machine Learning Pipelines har lagts till AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep ([notebook](https://aka.ms/pl-hyperdrive)) och tidsbaserade schemaläggning funktioner ([notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep uppdateras för att arbeta med Azure SQL Server och Azure database för PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Ändringar**
  + Inaktuell `PublishedPipeline.get_published_pipeline` för `PublishedPipeline.get`.
  + Inaktuell `Schedule.get_schedule` för `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nya funktioner**
  + Data Prep stöder nu läsa från en Azure SQL-databas med datalagret.
 
+ **Ändringar**
  + Förbättrats avsevärt minnesprestanda för vissa åtgärder på stora mängder data.
  + `read_pandas_dataframe()` kräver nu att `temp_folder` anges.
  + Den `name` egenskapen `ColumnProfile` har föråldrat – Använd `column_name` i stället.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning-SDK för Python v1.0.10

+ **Ändringar**: 
  + Azure ML-SDK har inte längre azure cli-paket som beroende. Mer specifikt har beroenden för azure-cli-core och azure-cli-profil tagits bort från azureml-kärna. Det här är den användare som påverkas av ändringar:
    + Om du utför ”az login” och sedan använda azureml-sdk, gör SDK: N webbläsare eller inloggning på enhet koden en gång till. Den använder inte några autentiseringsuppgifter tillstånd som skapats av ”az login”.
    + Azure CLI-autentisering, till exempel med hjälp av ”az login” använda _azureml.core.authentication.AzureCliAuthentication_ klass. Azure CLI-autentisering, göra _pip installerar azure cli-_ i Python-miljön där du har installerat azureml-sdk.
    + Om du gör ”az login” med ett huvudnamn för tjänsten för automation, bör du använda _azureml.core.authentication.ServicePrincipalAuthentication_ klassen som azureml-sdk inte använder autentiseringsuppgifter tillstånd som skapats av azure CLI. 

+ **Felkorrigeringar**: Den här versionen innehåller främst mindre felkorrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Förbered SDK v1.0.8

+ **Felkorrigeringar**
  + Avsevärt bättre prestanda för att få data profiler.
  + Mindre buggar rör felrapportering.
  
### <a name="azure-portal-new-features"></a>Azure-portalen: nya funktioner
+ Ny dra och släpp kartläggning upplevelse för rapporter. Användare kan dra en kolumn eller ett attribut från brunnen i diagramområdet där systemet automatiskt välja en lämplig diagramtyp för användaren baserat på vilken typ av data. Användare kan ändra typ till andra tillämpliga typer eller lägga till ytterligare attribut.

    Diagramtyper som stöds:
    - Linjediagram
    - Histogram
    - Liggande stapeldiagram
    - Låddiagram
    - Punktdiagram
    - Bubbeldiagram diagram
+ Portalen skapar nu dynamiskt rapporter för experiment. När en användare skickar en körning till ett experiment, genereras automatiskt en rapport med loggade mått och diagram för att tillåta jämförelse från olika körningarna. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning-SDK för Python v1.0.8

+ **Felkorrigeringar**: Den här versionen innehåller främst mindre felkorrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Förbered SDK v1.0.7

+ **Nya funktioner**
  + Förbättringar av datalager (beskrivs i [How-to-till-guide-datalager](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/how-to-guides/datastore.ipynb))
    + Möjlighet att läsa från och skriva till Azure-filresurser och ADLS-datalager i skala upp har lagts till.
    + När du använder datalager, stöder Dataförberedelser nu med autentisering av tjänstens huvudnamn i stället för interaktiv autentisering.
    + Stöd har lagts till för wasb och wasbs URL: er.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Förbered SDK v1.0.6

+ **Felkorrigeringar**
  + En bugg har åtgärdats med läsning från offentliga läsbara Azure Blob-behållare på Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning-SDK för Python v1.0.6
+ **Felkorrigeringar**: Den här versionen innehåller främst mindre felkorrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

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
  + [Exempel på bärbara datorer](https://aka.ms/aml-notebooks) uppdateras nu om du vill använda den nya hantera databearbetning.
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
  * *azureml.core.compute.AmlCompute* gör blir föråldrat följande klasser - *azureml.core.compute.BatchAICompute* och *azureml.core.compute.DSVMCompute*. Klassen senare tas bort i kommande versioner. Klassen AmlCompute har nu en enklare definition bara behöver en vm_size och max_nodes och skalar automatiskt ditt kluster från 0 till max_nodes när ett jobb skickas. Vår [exempel anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) har uppdaterats med den här informationen och bör ge dig användningsexempel. Vi hoppas att du som den här förenkling av distribution och mycket mer spännande funktioner kommer i en senare version!

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


## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning-tjänsten](../service/overview-what-is-azure-ml.md).
