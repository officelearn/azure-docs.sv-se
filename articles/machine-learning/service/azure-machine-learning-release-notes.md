---
title: Vad är nytt i Azure Machine Learning
description: Det här dokumentet beskriver uppdateringarna till Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 10/24/2018
ms.openlocfilehash: 6007a7e32e168ada529feb6aa24b8d572671d835
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291348"
---
# <a name="azure-machine-learning-service-release-notes"></a>Viktig information för Azure Machine Learning-tjänsten

I den här artikeln lär du dig om Azure Machine Learning-tjänstversioner. 

## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning-SDK för Python v0.1.80

+ **Större ändringar** 
  * *azureml.Train.widgets* namnområde har flyttats till *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* gör blir föråldrat följande klasser - *azureml.core.compute.BatchAICompute* och *azureml.core.compute.DSVMCompute*. Klassen senare tas bort i kommande versioner. Klassen AmlCompute har nu en enklare definition bara behöver en vm_size och max_nodes och skalar automatiskt ditt kluster från 0 till max_nodes när ett jobb skickas. Vår [exempelanteckningsböcker] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) har uppdaterats med den här informationen och bör ge dig exempel på hur du använder detta. Vi hoppas att du som den här förenkling av distribution och mycket mer spännande funktioner kommer i en senare version!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Förbered SDK v0.5.1 

Läs mer om Data Prep SDK genom att läsa [referera till docs](https://aka.ms/data-prep-sdk).
+ **Nya funktioner**
   * Skapa en ny DataPrep CLI för att köra paket för förberedelse av data och visa data för en datauppsättning eller dataflöde
   * Omarbetad SetColumnType API för att förbättra användbarhet
   * Omdöpt smart_read_file till auto_read_file
   * Innehåller nu skeva och datamängds i profilen för Data
   * Kan ta prov med stratified sampling
   * Kan läsa från zip-filer som innehåller CSV-filer
   * Dela upp datauppsättningar efter rader med slumpmässiga Split (t.ex. i test-train-uppsättningar)
   * Kan hämta kolumnen datatyper från en dataflöde eller en dataprofil för genom att anropa .dtypes
   * Kan få antalet rader som ett dataflöde eller en dataprofil för genom att anropa .row_count

+ **Felkorrigeringar**
   * Fast länge till dubbla konvertering 
   * Fast assert när någon Lägg till kolumn 
   * Ett problem har åtgärdats med FuzzyGrouping, där den inte kan identifiera grupper i vissa fall
   * Fast sorteringsfunktionen respekterar sorteringsordning för flera kolumner
   * Fast och/eller uttryck liknar hur Pandas hanterar dem.
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
  * *Workspace.compute_targets, datalager, experiment, bilder, modeller* och *webservices* egenskaper i stället för metoder. Ersätt till exempel *Workspace.compute_targets()* med *Workspace.compute_targets*.
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
  * Flera stöd för klienter när du skapar en ny arbetsyta.

+ **Korrigerade buggar**
  * Version för pynacl library behöver inte längre fästas när du distribuerar web-Molntjänsten.

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
  * Jobbet är nu korrekt Rapportstatus även om de avslutas med fel code än 0. 
  * RunConfig attributet validering i SDK. 
  * HyperDrive kör objektet stöder Avbryt liknar en vanlig körning: du behöver inte vidarebefordra alla parametrar. 
  * Widget förbättringar för att underhålla tillståndet för listrutan värden för distribuerade körningar och HyperDrive körs. 
  * TensorBoard och andra loggar som stöd för filer som fasta för parametern-server. 
  * Intel(R) MPI stöd på serversidan. 
  * Bugfix till parametern justering för distribuerade kör korrigering vid verifiering av i BatchAI. 
  * Kontext Manager identifierar nu den primära instansen. 

+ **Azure-portalen**
  * log_table() och log_row() stöds i informationen om körningen. 
  * Automatiskt skapa diagram för tabeller och rader med 1,2 eller 3 numeriska kolumner och en valfri kategoriska kolumn.

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
  * Lägga till en metod PipelineRun.get_pipeline_runs() att hämta pipelinen körs som har genererats från en publicerade pipeline.

+ **Project Brainwave**
  * Uppdaterade stöd för nya AI-modeller som finns på FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Förbered SDK v0.2.0
[Version 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) innehåller följande funktioner och bugfixes:

+ **Nya funktioner**
  * Stöd för en frekvent kodning
  * Stöd för quantile transformering
   
+ **Bugg har åtgärdats:**
  * Fungerar med en storm-version du behöver inte nedgradera din storm-version
  * Antal värden för alla värden, inte bara de tre överst

## <a name="2018-09-public-preview-refresh"></a>2018-09 (offentlig förhandsversion uppdatera)

En ny, helt uppdaterade versionen av Azure Machine Learning: Läs mer om den här versionen: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Tidigare anteckningar: september 2017 – Jun 2018
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

Med den här versionen av Azure Machine Learning kan du:
+ Funktionalisera bilder med en quantized version av ResNet-50, träna en klassificerare som baseras på dessa funktioner och [distribuerar den modellen till en FPGA på Azure](../service/how-to-deploy-fpga-web-service.md) för extremt låg latens inferensjobb.

+ Snabbt bygga och driftsätta mycket exakta machine learning och modeller för djupinlärning med [anpassade Azure Machine Learning-paket](../desktop-workbench/reference-python-package-overview.md) för följande domäner:
  + [Datorvision](../desktop-workbench/how-to-build-deploy-image-classification-models.md)
  + [Textanalys](../desktop-workbench/how-to-build-deploy-text-classification-models.md)
  + [Prognosticering](../desktop-workbench/how-to-build-deploy-forecast-models.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Versionsnumret**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Många av följande uppdateringar görs som direkt resultat av din feedback. Du nyårslöfte!

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
    - Använda entrypoints kan ändras
    - Översta filter kan läggas till och ändras ![Lägg till filter](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Diagram och statistik kan läggs till eller ändras (och dra och släpp om).
    ![Skapa nytt diagram](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD för Körningshistorik rapporter
  - Flytta alla befintliga kör historik-listvyn som konfigurationer på serversidan rapporter som fungerar som pipelines på körs från de valda startpunkterna.

- Fliken miljöer
  - Enkelt lägga till nya och köra konfigurationsfiler i projektet ![mål för nya Compute](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Hantera och uppdatera konfigurationsfilerna med hjälp av en enkel, formulärbaserad UX
  - Knapp för ny för att förbereda dina miljöer för körning

- Prestandaförbättringar i listan över filer i sidopanelen

#### <a name="data-preparation"></a>Förberedelse av data 
- Azure Machine Learning Workbench kan du nu för att kunna söka efter en kolumn med en känd kolumnnamn.


#### <a name="experimentation"></a>Experimentering
- Azure Machine Learning Workbench stöder nu köra dina skript internt på din egen python eller pyspark-miljö. För den här funktionen användaren skapar och hanterar sina egna miljö på Virtuellt fjärrdatorn, och använda Azure Machine Learning Workbench för att köra sina skript på målet. Se [konfigurerar Azure Machine Learning Experimentation Service](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Modellhantering
- Stöd för att anpassa de behållare som distribueras: gör det möjligt att anpassa behållaravbildningen genom att tillåta installation av externa bibliotek med apt-get etc. Det är inte längre begränsad till pip-installation-bibliotek. Se den [dokumentation](../desktop-workbench/model-management-custom-container.md) för mer information.
  - Använd den `--docker-file myDockerStepsFilename` flaggan och filnamnet med manifest, bild eller tjänst skapas kommandon.
  - Observera att basavbildningen är Ubuntu och kan inte ändras.
  - Exempel-kommando: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



### <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Versionsnumret**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Här följer uppdateringar och förbättringar i den här sprint. Många av de här uppdateringarna görs direkt följd av feedback från användare. 


Följande är en lista med detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

- Uppdateringar till stacken autentisering tvingar inloggning och konto markerad vid start

#### <a name="workbench"></a>Workbench
- Möjligheten att installera/avinstallera appen från Lägg till/ta bort program
- Uppdateringar till stacken autentisering tvingar inloggning och konto markerad vid start
- Förbättrad upplevelse för enkel inloggning (SSO) på Windows
- Användare som tillhör flera innehavare med olika autentiseringsuppgifter kommer nu att kunna logga in på Workbench

#### <a name="ui"></a>ANVÄNDARGRÄNSSNITTET
- Allmänna förbättringar och felkorrigeringar

#### <a name="notebooks"></a>Bärbara datorer
- Allmänna förbättringar och felkorrigeringar

#### <a name="data-preparation"></a>Förberedelse av data 
- Förbättrad automatisk-förslag när du utför med hjälp av exempel omvandlingar
- Förbättrade algoritmen för mönstret frekvens inspector
- Möjligheten att skicka exempeldata och feedback när du utför med hjälp av exempel transformationer ![bild av skicka feedbacklänk på Härled kolumn transformering](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Förbättringar av Spark-körning
- Scala har ersatt Pyspark
- Fast oförmåga att Stäng Data är inte tillämpligt för Time Series-Inspector 
- Fast låser sig tiden för Dataförberedelser körning för HDI

#### <a name="model-management-cli-updates"></a>Uppdaterar modellen Management CLI 
  - Ägarskapet för prenumerationen är inte längre behövs för att etablera resurser. Deltagaråtkomst till resursgruppen räcker att ställa in distributionsmiljö.
  - Aktiverade lokala miljön utan kostnad konfigurera prenumerationer 

### <a name="2017-12-sprint-2-qfe"></a>2017-12 (sprint 2 QFE) 
**Versionsnumret**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Det här är QFE (Quick Fix Engineering)-versionen har en lägre version. Den lösningar på problemen flera telemetri och hjälper produktteam för att bättre förstå hur produkten används. Informationen kan gå in framtida ansträngningar för att förbättra upplevelsen för produkten. 

Dessutom finns två viktiga uppdateringar:

- Ett fel har åtgärdats i dataförberedelser som förhindrade att time series inspector visas i paket för förberedelse av data.
- I kommandoradsverktyget behöver du inte längre vara Azure-prenumerationsägare etablera Machine Learning Compute ACS-kluster. 

### <a name="2017-12-sprint-2"></a>2017-12 (sprint 2)
**Versionsnumret**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Välkommen till den tredje uppdateringen av Azure Machine Learning. Den här uppdateringen innehåller förbättringar i workbench-appen och kommandoradsgränssnittet (CLI) backend-tjänster. Tack så mycket för att skicka leenden och frowns. Många av följande uppdateringar görs som direkt resultat av din feedback. 

**Viktiga nya funktioner**
- [Stöd för SQL Server och Azure SQL DB som en datakälla](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 
- [Djupinlärning på Spark med GPU-stöd med MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Alla AML-behållare som är kompatibla med Azure IoT Edge-enheter när de distribueras (inga ytterligare åtgärder krävs)](https://aka.ms/aml-iot-edge-blog)
- Registrerade modellen lista och information om vyer tillgängliga Azure-portalen
- Åtkomst till beräkningsmål med hjälp av SSH-nyckel-baserad autentisering förutom användarnamn/lösenord-baserad åtkomst. 
- Nya mönster frekvens Inspector i data Förbered upplevelse. 

**Detaljerad uppdateringar** följer en lista med detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

#### <a name="installer"></a>Installationsprogrammet
- Installationsprogrammet kan självsignerat uppdateringen så som buggar korrigeringar och nya funktioner kan användas utan att användaren behöver installera det på nytt

#### <a name="workbench-authentication"></a>Workbench-autentisering
- Flera korrigeringar av autentiseringssystem. Kontakta oss gärna om du fortfarande har problem med inloggningen.
- Ändringar i Användargränssnittet som gör det lättare att hitta inställningarna för Proxyhanteraren.

#### <a name="workbench"></a>Workbench
- Skrivskyddad vy har nu ljusblå bakgrund
- Flyttade redigera-knappen till höger för att göra det enklare att hitta.
- ”dsource”, ”dprep” och ”ipynb”-filformat kan nu återges i rå text-format
- Workbench har nu en ny redigering upplevelse som hjälper användarna mot med externa IDE: er för att redigera skript och Använd Workbench endast om du vill redigera filtyper som har en omfattande redigering upplevelse (till exempel bärbara datorer, datakällor, paket för förberedelse av Data)
- Inläsning av listan med arbetsytor och projekt som användaren har åtkomst till är betydligt snabbare nu

#### <a name="data-preparation"></a>Förberedelse av data 
- En mönstret frekvens Inspector att visa mönster som sträng i en kolumn. Du kan också filtrera dina data med dessa mönster. Detta visar vyn liknar inspector antal värden. Skillnaden är att mönstret frekvens visar antalet unika mönster av data, snarare än antalet unika data. Du kan också filtrera in eller ut alla rader som motsvarar ett visst mönster.

![Bild av mönstret frekvens inspector på produktnumret](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Prestandaförbättringar vid rekommenderar gränsfall att granska i ”Härled kolumner med exempel”-transformering

- [Stöd för SQL Server och Azure SQL DB som en datakälla](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 

![Bild för att skapa en ny SQL server-datakälla](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Aktiverat ”i korthet” vy över antal rad och kolumn

![Bild av radantal kolumn på en respitperiod](media/azure-machine-learning-release-notes/row-col-count.png)

- Dataförberedelser är aktiverad i alla beräkningskontext
- Datakällor som använder en SQL Server-databas är aktiverade i alla beräkningskontext
- Data Förbered grid kolumner kan filtreras efter datatyp
- Ett problem har åtgärdats med att konvertera flera kolumner till dags datum
- Ett problem har åtgärdats kan som användaren välja utdatakolumnen som källa i Härled kolumn genom att exempel om användaren har ändrat utdata kolumnnamnet i Avancerat läge.

#### <a name="job-execution"></a>Jobbkörning
Du kan nu skapa och få åtkomst till en remotedocker eller ett kluster typ beräkningsmål med hjälp av SSH-nyckel-baserad autentisering följa dessa steg:
- Koppla beräkningsmål med följande kommando i CLI

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (eller--Använd-azureml-ssh-key) alternativet i kommandot anger för att skapa och använda SSH-nyckel.

- Azure ML Workbench ska generera en offentlig nyckel och utdata som i konsolen. Logga in beräkningsmål som använder samma användarnamn och Lägg till ~/.ssh/authorized_keys filen med den offentliga nyckeln.

- Du kan förbereda den här beräkningsmål och använda det för körning och Azure ML Workbench använder den här nyckeln för autentisering.  

Läs mer om hur du skapar beräkningsmål [konfigurerar Azure Machine Learning Experimentation Service](../desktop-workbench/experimentation-service-configuration.md)

#### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools för AI
- Lagt till stöd för [Visual Studio Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnitt (CLI)
- Lagt till `az ml datasource create` kommandot tillåter att skapa en från kommandoraden

#### <a name="model-management-and-operationalization"></a>Modellhantering och driftsättning
- [Alla AML-behållare som är kompatibla med Azure IoT Edge-enheter när operationaliserat (inga ytterligare åtgärder krävs)](https://aka.ms/aml-iot-edge-blog) 
- Förbättringar av felmeddelanden i o16n CLI
- Felkorrigeringar i hanteringsportalen för modellen UX  
- Konsekvent bokstav skiftläge för modellen management attribut i den här sidan
- I realtid bedömning anrop timeout inställt på 60 sekunder
- Registrerade modellen lista och information om vyer som är tillgängliga i Azure portal

![modelldetaljer i portalen](media/azure-machine-learning-release-notes/model-list.jpg)

![Översikt över säkerhetsmodell i portalen](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

#### <a name="mmlspark"></a>MMLSpark
- Deep Learning på Spark med [GPU-stöd](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Stöd för Resource Manager-mallar för enkelt resursdistributionen
- Stöd för SparklyR-ekosystem
- [AZTK-integrering](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

#### <a name="sample-projects"></a>-Exempelprojekt
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) och [MMLSpark](https://github.com/Azure/mmlspark) exempel uppdateras med den nya Azure ML SDK-versionen

#### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
- Upphöja den `--type` växel i `az ml computetarget attach` till underkommandot a. 

    - `az ml computetarget attach --type remotedocker` är nu `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` är nu `az ml computetarget attach cluster`

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Versionsnumret**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vi har gjort förbättringar kring säkerhet, stabilitet och underhåll i workbench-appen och CLI lagret backend-tjänster i den här versionen. Tack mycket för att skicka oss leenden och frowns. Många av de nedan uppdateringar görs som direkt resultat av din feedback. Nyårslöfte!

#### <a name="notable-new-features"></a>Viktiga nya funktioner
- Azure ML är nu tillgängligt i två nya Azure-regioner: **Västeuropa** och **Sydostasien**. De ansluta till de föregående regionerna **östra USA 2**, **USA, västra centrala**, och **östra**, tar det totala antalet distribueras regioner till fem.
- Vi har aktiverat Python-kod för syntaxmarkering i Workbench-appen för att göra det enklare att läsa och redigera källkoden för Python. 
- Nu kan du starta din favorit-IDE direkt från en fil i stället för från hela projektet.  Öppna en fil i Workbench och klicka på ”Redigera” startar din IDE (för närvarande VS Code och PyCharm stöds) till aktuella och projekt.  Du kan också klicka på pilen bredvid knappen Redigera Redigera filen i textredigeraren Workbench.  Filerna är skrivskyddade förrän du klickar på Redigera, förhindrar oavsiktliga ändringar.
- Populära ritområdet biblioteket `matplotlib` version 2.1.0 ingår nu i Workbench-appen.
- Vi har uppgraderat .NET Core-version till 2.0 för Förberedelseverktyg engine-data. Detta bort behovet av brew-installera openssl under installationen av appen på macOS. Den banar också vägen för mer spännande data prep funktioner för att inom en snar framtid. 
- Vi har aktiverat en versionsspecifika appens startsida, så att du får mer relevant viktig information och uppdatera anvisningarna baserat på din nuvarande version av appen.
- Om din lokala användarnamnet har ett utrymme i den, kan programmet nu installeras. 

#### <a name="detailed-updates"></a>Detaljerad uppdateringar
Nedan visas en lista med detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

##### <a name="installer"></a>Installationsprogrammet
- Appinstallationsprogram rensar nu installationskatalogen som skapats av äldre version av appen.
- Ett fel som leder installer fastnar vid 100% i macOS High Sierra har åtgärdats.
- Det finns nu en direktlänk till installationsprogrammet för katalogen för användare att granska installationsloggar om installationen misslyckas.
- Installera nu fungerar för användare som har utrymme i deras användarnamn.

##### <a name="workbench-authentication"></a>Workbench-autentisering
- Stöd för autentisering i Proxyhanteraren.
- Logga in nu lyckas om användaren är bakom en brandvägg. 
- Om användaren har experimenteringskonton i flera Azure-regioner, och om en region råkar vara otillgänglig, appen låser sig inte längre.
- När autentisering inte har slutförts och dialogrutan för autentisering är fortfarande är synlig, försöker app inte längre att läsa in arbetsyta från den lokala cachen.

##### <a name="workbench-app"></a>Workbench-appen
- Syntaxmarkering för Python-kod är aktiverat i en textredigerare.
- Knappen Redigera i textredigeraren kan du redigera filen i en IDE-miljö (VS Code och PyCharm stöds) eller i inbyggda textredigeraren.
- Textredigerare är i skrivskyddat läge som standard. 
- Spara knappen visual nu tillståndsändringar inaktiverad när den aktuella filen är sparad och kan därför inte längre är felaktig.
- Workbench sparar _alla_ som inte sparats filer när du startar en körning.
- Workbench kommer ihåg senast använda arbetsyta på den lokala datorn så att den öppnas automatiskt.
- Endast en instans av Workbench har nu behörighet att köras. Tidigare kan flera instanser startas som orsakas av problem när fungerar på samma projekt.
- Omdöpt Arkiv-menyn ”Öppna projekt...” till ”Lägg till befintlig mapp som projekt...” 
- Fliken växlar är nu mycket snabbare.
- Hjälplänkar läggs till i dialogrutan Konfigurera IDE.
- Feedback-formulär nu ihåg den e-postadress som du har angett senast.
- Leenden och frowns formuläret textområde är nu större, så att du kan skicka oss mer feedback! 
- Den `--owner` växla hjälptext i `az ml workspace create` korrigeras.
- Vi har lagt till en ”om” dialogruta för att hjälpa användare enkelt visa och kopiera versionsnumret för appen.
- Ett ”föreslå en funktion” menyalternativ har lagts till på Hjälp-menyn.
- Kontonamnet för experimentering visas nu i appen namnlisten och föregående appnamnet ”Azure Machine Learning Workbench”.
- En versionsspecifika appens startsida visas nu baserat på vilken version av appen har identifierats.

##### <a name="data-preparation"></a>Förberedelse av data 
- Extern webbplats kan inte längre läsas från Kartkontrollen att förhindra potentiella säkerhetsproblem.
- Histogram och antal värden inspectors har nu möjlighet att visa diagram i logaritmisk skala.
- När en beräkning är pågående, datakvalitetsstapeln nu visar en annan färg för att signalera ”beräkning”-tillstånd.
- Kolumnen mått kan nu visa statistik för kolumner med kategoriska värden.
- Det sista tecknet i namnet på datakällan är inte längre trunkeras.
- Databearbetningspaketet är nu öppen när du växlar flikar, vilket märkbar prestandavinster.
- I datakällan, när du växlar mellan datavyn och måttvy ändras ordningen på kolumnerna nu längre.
- Öppna en ogiltig `.dprep` eller `.dsource` filen orsakar inte längre Workbench ska krascha.
- Databearbetningspaketet kan nu använder relativ sökväg för utdata i _skriva till CSV_ omvandla.
- _Behåll kolumnen_ transformeringen nu kan användaren att lägga till fler kolumner när redigeras.
- _Ersätt detta_ menyn nu faktiskt startar _ersätta värdet_ dialogrutan.
- _Ersätt värdet_ omvandla nu funktioner som förväntat i stället för att fel.
- Databearbetningspaketet använder nu absolut sökväg när du refererar till filer utanför projektmappen, vilket gör det möjligt att köra paketet i lokal kontext med absolut sökväg till filen.
- _Fullständig fil_ som en samplingsstrategi stöds nu när du använder Azure blob som datakälla.
- Genererade Python-kod (från databearbetningspaketet) har nu både CR och LF, vilket gör det egna i Windows.
- _Välj mätvärden_ listrutan döljer nu egenskapen när du växlar till datavyn.
- Workbench kan du nu processen parquet-filer även när den använder Python-körning. Tidigare kan endast Spark användas vid bearbetning av parquet-filer. 
- Filtrera bort värdena i en kolumn med _datum_ datatyp orsakar inte längre Förberedelseverktyg motor ska krascha.
- Måttvy respekterar nu sampling strategi för uppdateringar.
- Remote sampling jobb nu fungerar korrekt.

##### <a name="job-execution"></a>Jobbkörning
- Argumentet ingår nu i post i körningshistoriken.
- Jobb som startats CLI nu visas i panelen jobb körs automatiskt.
- Jobbet panelen visas nu jobben som skapats av gästanvändare som lagts till i Azure AD-klient.
- Avbryt jobbet panelen och borttagna åtgärder är mer stabil.
- När du klickar på Kör, utlöses ett felmeddelande nu om filerna finns i felaktigt format.
- Avslutande app stör inte längre jobb som startats i CLI.
- Jobb som startats CLI nu fortsätter att dela ut standard ut även efter en timme för körning.
- Bättre felmeddelanden visas när databearbetningspaketet körs inte i Python/PySpark.
- `az ml experiment clean` nu Rensar upp Docker-avbildningar i fjärransluten virtuell dator också.
- `az ml experiment clean` nu fungerar korrekt för lokala mål på macOS.
- Felmeddelanden när riktar in sig på lokala eller fjärranslutna Docker körs rensas upp och lättare att läsa.
- Bättre felmeddelande visas när HDInsight-klustrets huvudnod namn inte har utformats korrekt när ansluten som en körningsmål.
- Bättre felmeddelande visas när hemlighet inte hittades i tjänsten autentiseringsuppgifter. 
- MMLSpark-bibliotek är uppgraderade till support Apache Spark 2.2.
- MMLSpark innehåller nu ämne kodning transformering (nät kodning) för medicinska dokument.
- `matplotlib` version 2.1.0 är nu levererade out-nyckelfärdig med Workbench.

##### <a name="jupyter-notebook"></a>Jupyter Notebook
- Anteckningsboken namnsökning fungerar nu korrekt i vyn anteckningsböcker.
- Du kan nu ta bort en anteckningsbok i vyn anteckningsböcker.
- Ny magic `%upload_artifact` har lagts till för överföringen av filer skapas i körningsmiljön anteckningsboken i körningshistoriken datalager.
- Kernel-fel visas nu i anteckningsboken jobbstatus för enklare felsökning.
- Jupyter server nu avslutas korrekt när användaren loggar ut från appen.

##### <a name="azure-portal"></a>Azure Portal
- Konto för experimentering och modellhantering konto kan nu skapas i två nya Azure-regioner: västra Europa och Asien, sydöstra.
- Modellen konto plan för labb för utveckling och nu finns bara när det är den första som skapas i prenumerationen. 
- Hjälplänk i Azure-portalen uppdateras för att peka mot korrekt dokumentationssidan.
- Beskrivningsfältet tas bort från informationssidan för Docker-avbildningen eftersom den inte är tillämplig.
- Information, inklusive inställningar för AppInsights och Autoskala läggs till sidan web service.
- Sidan för hantering av modellen återger nu även om cookies från tredje part är inaktiverade i webbläsaren. 

##### <a name="operationalization"></a>Driftsättning
- Webbtjänsten med ”poäng” i namnet inte längre.
- Användare kan nu skapa en distributionsmiljö med bara deltagaråtkomst till en Azure-resursgrupp eller prenumerationen. Ägaråtkomst till hela prenumerationen är inte längre behövs.
- Driftsättning CLI nu gillar fliken automatisk komplettering i Linux.
- Bild-konstruktion tjänst har nu stöd för att skapa avbildningar för Azure IoT-tjänster/enheter.

##### <a name="sample-projects"></a>-Exempelprojekt
- [_Klassificera Iris_ ](../desktop-workbench/tutorial-classifying-iris-part-1.md) exempelprojektet:
    - `iris_pyspark.py` har bytt namn till `iris_spark.py`.
    - `iris_score.py` har bytt namn till `score_iris.py`.
    - `iris.dprep` och `iris.dsource` uppdateras för att avspegla de senaste uppdateringarna för data prep-motorn.
    - `iris.ipynb` Anteckningsboken ändras för att fungera i HDInsight-klustret.
    - Kör historik är aktiverat i `iris.ipynb` Notebook cell.
- [_Avancerade Dataförberedelser med hjälp av Bikeshare-Data_ ](../desktop-workbench/tutorial-bikeshare-dataprep.md) exempelprojektet ”hantera fel Value” steg har åtgärdats.
- [_MMLSpark på vuxet insamlade Data_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) exempelprojektet `docker.runconfig` format uppdateras från JSON till YAML.
- [_Distribuerade finjustering av Hyperparametrar_ ](../desktop-workbench/scenario-distributed-tuning-of-hyperparameters.md) exempelprojektet`docker.runconfig` format uppdateras från JSON till YAML.
- Ny exempelprojektet [ _klassificering av avbildning med hjälp av CNTK_](../desktop-workbench/scenario-image-classification-using-cntk.md).


### <a name="2017-10-sprint-0"></a>2017-10 (sprint 0) 
**Versionsnumret**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Välkommen till den första uppdateringen av Azure Machine Learning Workbench följa vår första offentlig förhandsversion på Microsoft Ignite 2017-konferensen. De viktigaste uppdateringarna i den här versionen är tillförlitlighet och stabiliserade korrigeringar.  Några av de viktiga problem vi är:

#### <a name="new-features"></a>Nya funktioner
- macOS High Sierra stöds nu

#### <a name="bug-fixes"></a>Felkorrigeringar
##### <a name="workbench-experience"></a>Workbench-upplevelse
- Dra och släpp en fil i Workbench orsaker Workbench ska krascha.
- Terminalfönstret i VS Code som konfigurerats som en IDE för inte känner igen Workbench _az ml_ kommandon.

##### <a name="workbench-authentication"></a>Workbench-autentisering
Vi har gjort ett antal uppdateringar för att förbättra olika inloggning och autentisering problem som har rapporterats.
- Autentisering fönstret behåller dyker upp, särskilt när Internetanslutningen inte är stabil.
- Förbättrad tillförlitlighetsproblem runt autentiseringstokens.
- I vissa fall kan visas autentisering fönster två gånger.
- Workbench huvudfönstret visar fortfarande ”autentisera” visas när autentiseringen är klar och popup-dialogrutan redan stängs.
- Om det finns ingen Internetanslutning, autentiseringsdialogen som öppnas med ett tomt fönster.

##### <a name="data-preparation"></a>Förberedelse av data 
- När ett specifikt värde filtreras, fel och värden som saknas också filtreras bort.
- Ändra en samplingsstrategi tar bort efterföljande befintliga kopplingsåtgärder.
- Ersätt saknas värdet tar transformeringen inte NaN i beräkningen.
- Datum typ inferens utlöser undantag när null-värde påträffades.

##### <a name="job-execution"></a>Jobbkörning
- Det finns inga Rensa felmeddelande när jobbet körs inte kan ladda upp projektmapp, till exempel eftersom den överskred storleksgränsen.
- Om användarens Python-skriptet ändras arbetskatalogen, spåras inte filerna som skrivits till utdata-mappar. 
- Om aktiv Azure-prenumeration är samma som det konto som tillhör det aktuella projektet, resulterar jobböverföring ett 403-fel.
- Om Docker inte finns returneras utan tydliga felmeddelande om användaren försöker använda Docker som ett körningsmål.
- .runconfig filen sparas inte automatiskt när användaren klickar på _kör_ knappen.

##### <a name="jupyter-notebook"></a>Jupyter Notebook
- Notebook-server kan inte starta om användaren använder med vissa typer av inloggningen.
- Felmeddelanden för notebook-server inte visa upp i loggar som är synliga för användaren.

##### <a name="azure-portal"></a>Azure Portal
- Att välja det mörka temat Azure-portalen gör modellhantering bladet ska visas som en svart ruta.

##### <a name="operationalization"></a>Driftsättning
- Återanvända ett manifest för att uppdatera en webbtjänst leder till en ny Docker-avbildning som skapats med ett slumpmässigt namn.
- Webbloggar för tjänsten kan inte hämtas från Kubernetes-kluster.
- Vilseledande felmeddelandet skrivs ut när användare försöker skapa ett modellhanteringskonto eller en ML beräkningskonto och påträffar behörighetsproblem.

## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
