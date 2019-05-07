---
title: Vad är nytt i version?
titleSuffix: Azure Machine Learning service
description: 'Läs om de senaste uppdateringarna för Azure Machine Learning-tjänsten och den machine learning och data Förbered Python SDK: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: a355d18de875ad980e0c2b6c564d3379c2b90ee7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154294"
---
# <a name="azure-machine-learning-service-release-notes"></a>Viktig information för Azure Machine Learning-tjänsten

I den här artikeln lär du dig om Azure Machine Learning-tjänstversioner.  En fullständig beskrivning av varje SDK finns i referensdokument för:
+ Azure Machine Learning [ **huvudsakliga SDK för Python**](https://aka.ms/aml-sdk)
+ Azure Machine Learning [ **Data Förbered SDK**](https://aka.ms/data-prep-sdk)

Se [lista över kända problem](resource-known-issues.md) att lära dig om kända fel och lösningar.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

I Azure-portalen kan du nu:
+ Skapa och köra automatiserade ML-experiment 
+ Skapa en anteckningsbok virtuell dator för att testa exempel Jupyter-anteckningsböcker eller dina egna.
+ Helt nya redigering avsnittet (förhandsversion) på arbetsytan Machine Learning-tjänsten, som omfattar automatiserad Machine Learning, visuella gränssnittet och värdbaserade Notebook virtuella datorer
    + Automatiskt skapa en modell som använder automatisk machine learning 
    + Använda en dra och släpp visuella gränssnittet för att köra experiment
    + Skapa en anteckningsbok virtuell dator för att utforska data, skapa modeller och distribuera tjänster.
+ Live-diagram och mått som uppdaterar i kör rapporter och kör sidor med appinformation
+ Uppdaterade filen visningsprogram för loggar, utdata och ögonblicksbilder för sidor med appinformation kör.
+ Skapa en ny och förbättrad rapporten användarupplevelsen i fliken experiment. 
+ Möjlighet att hämta config.json-filen från sidan Översikt över arbetsytan Azure Machine Learning-tjänsten har lagts till.
+ Stöd för Machine Learning-tjänsten arbetsytan skapas från Azure Databricks-arbetsyta 


### <a name="notebook-virtual-machine"></a>Anteckningsboken virtuell dator 

Använda en Notebook virtuell dator som en säker, företagsfärdig värdmiljö för Jupyter-anteckningsböcker som du kan programmera machine learning-experiment, distribuera modeller som webb-slutpunkter och utföra andra åtgärder som stöds av Azure Machine Learning-SDK med hjälp av Python. Den tillhandahåller flera funktioner:
+ [Skapa snabbt en förkonfigurerade VM-anteckningsbok](quickstart-run-cloud-notebook.md) som har den senaste versionen av Azure Machine Learning SDK och relaterade paket.
+ Åtkomst skyddas via beprövade tekniker, till exempel HTTPS, Azure Active Directory-autentisering och auktorisering.
+ Pålitlig och kostnadseffektiv molnlagring i anteckningsböcker och koden i Azure Machine Learning-arbetsyta blob storage-kontot. Du kan ta bort anteckningsboken VM utan att förlora ditt arbete.
+ Förinstallerad exempelanteckningsböcker att utforska och experimentera med funktioner för Azure Machine Learning-tjänsten.
+ Funktioner för fullständig anpassning av virtuella Azure-datorer, alla VM-typer, eventuella paket, alla drivrutiner. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK för Python v1.0.33 släpps.

+ Azure ML maskinvara Accelerated modeller på [FPGA](concept-accelerate-with-fpgas.md) är allmänt tillgänglig.
  + Du kan nu [använder azureml-acceleration-modeller paketet](how-to-deploy-fpga-web-service.md) till:
    + Träna vikten av ett stöds djupa neurala nätverk (ResNet-50, ResNet 152, DenseNet 121, VGG-16- och SSD-VGG)
    + Använda induktiv inlärning med stöds DNN
    + Registrera modellen med Modellhanteringstjänsten och behållaranpassa modellen
    + Distribuera modellen till en Azure-dator med en FPGA i ett kluster i Azure Kubernetes Service (AKS)
  + Distribuera behållaren till en [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server enhet
  + Bedöma dina data med gRPC slutpunkten med den här [exempel](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

+ Funktionen omfattande för att aktivera dynamiskt lägga till featurizers för prestandaoptimering. Ny featurizers: fungerar inbäddningar vikten för bevis, mål-kodning, textkodning mål, kluster avstånd
+ Smart KA att hantera träna/giltig delar upp i automatiserade ML
+ Några ändringar i minnet optimering och prestandaförbättringar för körning
+ Förbättring av prestanda i modellen förklaring
+ Konvertering av ONNX-modellen för lokal körning
+ Stöd har lagts till Subsampling
+ Intelligent stoppas när inga avsluta kriterier har definierats
+ Stående ensembler

+ Time Series prognoser
  + Ny förutsäga prognoser funktion   
  + Du kan nu använda löpande ursprung mellan verifiering på time series-data
  + Nya funktioner som lagts till att konfigurera time series ligger 
  + Nya funktioner har lagts till för rullande fönster aggregerade funktioner
  + Ny helgdag identifiering och upplärda när standardlandskod har definierats i experimentera inställningar

+ Azure Databricks
  + Aktiverat med tidsserier och modellera explainabilty/interpretability funktion
  + Nu kan du avbryta och återuppta (Fortsätt) automatiserad ML-experimenten
  + Stöd har lagts till för flerkärniga bearbetning

### <a name="mlops"></a>MLOps
+ **Lokal distribution och felsökning för bedömning behållare**<br/> Du kan nu distribuera en ML-modellen lokalt och iterera snabbt på bedömnings-fil och beroenden för att se till att de fungerar som förväntat.

+ **Introduced InferenceConfig & Model.deploy()**<br/> Nu modeller distributionen har stöd för att ange en målmapp med en post-skript, samma som en RunConfig.  Dessutom har modelldistribution förenklats till ett enda kommando.

+ **Spåra Git-referens**<br/> Kunder har begär grundläggande funktioner för Git-integrering för en stund eftersom det hjälper till att upprätthålla granskningsspårning slutpunkt till slutpunkt. Vi har implementerat spårning i större enheter i Azure ML för Git-relaterade metadata (lagringsplatsen, commit, rent tillstånd). Den här informationen samlas in automatiskt av SDK och CLI.

+ **Modeller profilering och verifiering service**<br/> Kunder ofta börjar klaga för svårt att korrekt storleksanpassa beräkningarna som är associerad med deras inferens-tjänst. Med vår modell som Profileringen service kunden kan ge Exempelindata och vi kommer profilen på 16 olika CPU / minneskonfigurationer för att fastställa optimal storlek för distribution.

+ **Ta med din egen basavbildning för inferens**<br/> Ett annat vanligt klagomål var svårigheterna med att flytta från experimentering till inferens RE delningsapplikationen beroenden. Med vår nya basavbildningen delning funktionen återanvända du nu dina experimentering Källavbildningen, beroenden och alla för inferens. Detta bör snabbare distributioner och minska mellanrummet från inre till den yttersta slingan.

+ **Förbättrad upplevelse för generering av Swagger-schemat**<br/> Våra tidigare swagger-generationen metoden inträffade felbenägna och omöjligt att automatisera. Vi har ett nytt infogade sätt att generera swagger scheman från en Python-funktion via dekoratörer. Vi har öppen källkod den här koden och våra protokoll för generering av schemat kopplas inte för Azure ML-plattformen.

+ **Azure ML CLI är allmänt tillgänglig (GA)**<br/> Modeller kan nu distribueras med ett enda CLI-kommando. Vi fick vanliga kundfeedback som ingen distribuerar en ML-modell från en Jupyter-anteckningsbok. Den [ **CLI referensdokumentation** ](https://aka.ms/azmlcli) har uppdaterats.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK för Python v1.0.30 släpps.

Den [ `PipelineEndpoint` ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) har introducera för att lägga till en ny version av en publicerad pipeline samtidigt som du behåller samma slutpunkt.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Förbered SDK v1.1.2

Obs! Data Prep Python SDK: N kommer inte längre installera `numpy` och `pandas` paket. Se [uppdateras Installationsinstruktioner](https://aka.ms/aml-data-prep-installation).

+ **Nya funktioner**
  + Du kan nu använda Pivot-transformeringen.
    + Här guiden: [Pivot-anteckningsbok](https://aka.ms/aml-data-prep-pivot-nb)
  + Du kan nu använda reguljära uttryck i ursprungliga funktioner.
    + Exempel:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Du kan nu använda `to_upper`  och `to_lower`  funktioner i Uttrycksspråk.
  + Du kan nu se antalet unika värden för varje kolumn i en dataprofil.
  + För några av de vanliga läsare steg, kan du nu skicka den `infer_column_types` argumentet. Om den är inställd på `True`, Dataförberedelser kommer att försöka identifiera och automatiskt konvertera kolumntyper.
    + `inference_arguments` är nu föråldrad.
  + Nu kan du anropa `Dataflow.shape`.

+ **Felkorrigeringar och förbättringar**
  + `keep_columns` accepterar nu ett valfritt argument `validate_column_exists`, som kontrollerar om resultatet av `keep_columns` innehåller några kolumner.
  + Alla läsare steg (som läses från en fil) accepterar nu ett valfritt argument `verify_exists`.
  + Förbättrad prestanda för att läsa från pandas-dataframe och hämta data profiler.
  + Ett fel har åtgärdats där uppdelning av ett enda steg från ett dataflöde misslyckades med ett enda index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Du kan nu skicka ett befintligt skript som körs på en befintlig remote beräkningskluster. 
  + Du kan nu köra en publicerade pipeline med nya parametrar på fliken Pipelines. 
  + Körningsinformation har nu stöd för en ny ögonblicksbild-granskaren. Du kan visa en ögonblicksbild av katalogen när du har skickat in en specifik körning. Du kan också hämta anteckningsboken som skickades för att starta körningen.
  + Nu kan du avbryta överordnade körs från Azure-portalen.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning-SDK för Python v1.0.23

+ **Nya funktioner**
  + Azure Machine Learning SDK har nu stöd för Python 3.7.
  + Azure Machine Learning DNN Estimators ger nu inbyggt stöd för flera versioner. Till exempel `TensorFlow`  kostnadsuppskattning accepterar nu ett `framework_version` parameter och användarna kan ange version ”1.10' eller '1.12'. En lista över de versioner som stöds av din aktuella SDK-version, anropa `get_supported_versions()` på den önskade framework-klassen (till exempel `TensorFlow.get_supported_versions()`).
  En lista över versioner som stöds av den senaste versionen av SDK finns i den [DNN kostnadsuppskattning dokumentation](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Förbered SDK v1.1.1

+ **Nya funktioner**
  + Du kan läsa flera datalager/DataPath/DataReference källor med read_ * transformeringar.
  + Du kan utföra följande åtgärder på kolumner för att skapa en ny kolumn: division, våning modulo kraft, längd.
  + Dataförberedelser är nu en del av paketet för Azure ML-diagnostik och loggar diagnostikinformation som standard.
    + Ange variabeln till true om du vill inaktivera detta: DISABLE_DPREP_LOGGER

+ **Felkorrigeringar och förbättringar**
  + Förbättrad code-dokumentation för vanliga klasser och -funktioner.
  + Ett fel har åtgärdats i auto_read_file som inte gick att läsa Excel-filer.
  + Har lagts till med alternativet för att skriva över mappen i read_pandas_dataframe.
  + Förbättrad prestanda för dotnetcore2 beroendeinstallation och extra support för Fedora 27/28 och Ubuntu 1804.
  + Förbättrad prestanda för läsning från Azure Blobs.
  + Kolumnen typidentifiering nu stöder kolumner av typen Long.
  + Ett fel har åtgärdats där vissa datumvärden visades som tidsstämplar i stället för Python datetime-objekt.
  + Ett fel har åtgärdats där vissa typ antal visades som Double-värden i stället för heltal.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning-SDK för Python v1.0.21

+ **Nya funktioner**
  + Den *azureml.core.Run.create_children* metoden kan låg latens skapandet av flera underordnade körs med ett enda anrop.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Större ändringar**
  + Begreppet Prep datapaketet är inaktuell och längre stöds inte. I stället för att spara flera dataflöden i ett paket kan du behålla dataflöden individuellt.
    + Här guiden: [Anteckningsboken för att öppna och spara dataflöden](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nya funktioner**
  + Dataförberedelser kan nu identifiera kolumner som matchar en viss semantiska typ och dela i enlighet med detta. Inkludera STypes som stöds för närvarande: e-postadressen, geografiska koordinater (latitud och longitud), IPv4 och IPv6-adresser, Amerikanskt telefonnummer och postnummer.
    + Här guiden: [Den semantiska typer notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Dataförberedelser har nu stöd för följande åtgärder för att generera en gällande kolumn från två numeriska kolumner: subtrahera, multiplicera och dividera, och modulo.
  + Du kan anropa `verify_has_data()` på en dataflöde för att kontrollera om dataflödet skulle skapa poster om körs.

+ **Felkorrigeringar och förbättringar**
  + Du kan nu ange hur många lagerplatser ska användas i ett histogram för numerisk kolumn profiler.
  + Den `read_pandas_dataframe` transformeringen kräver nu DataFrame ha sträng - eller byte typifierade kolumnnamn.
  + En bugg i den `fill_nulls` transformering, där värdena inte har korrekt fylls i om kolumnen saknades.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning-SDK för Python v1.0.18

 + **Ändringar**
   + Azureml-tensorboard paketet ersätter azureml-contrib-tensorboard.
   + Den här versionen kan ställa du in ett användarkonto på ditt hanterade beräkningskluster (amlcompute) när du skapar den. Detta kan göras genom att skicka de här egenskaperna i konfigurationen för etablering. Du hittar mer information finns i den [SDK referensdokumentation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nya funktioner**
  + Nu har stöd för att lägga till två numeriska kolumner för att generera en gällande kolumn med hjälp av uttryck som språket.

+ **Felkorrigeringar och förbättringar**
  + Förbättrat dokumentationen och söker efter random_split-parametern.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Förbered SDK v1.0.16

+ **Felkorrigering**
  + Problem har åtgärdats ett huvudnamn för tjänsten autentisering som orsakades av en ändring av API: et.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning-SDK för Python v1.0.17

+ **Nya funktioner**

  + Azure Machine Learning har nu förstklassigt stöd för populära DNN-ramverk Chainer. Med hjälp av [ `Chainer` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) klassen användare enkelt kan träna och distribuera Chainer modeller.
    + Lär dig hur du [kör distribuerad utbildning med ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Lär dig hur du [kör finjustering av hyperparametrar med Chainer med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines lagt till möjligheten utlösare som en Pipeline som körs baserat på ändringar av datalager. Pipelinen [schema notebook](https://aka.ms/pl-schedule) har uppdaterats för att demonstrera den här funktionen.

+ **Felkorrigeringar och förbättringar**
  + Vi har lagt till stöd för Azure Machine Learning Pipelines för att ange egenskapen source_directory_data_store till en önskad datalager (till exempel en bloblagring) på [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) som skickas till den [ PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Som standard använder stegen Azure-lagring som säkerhetskopiering datalager, vilket stöta begränsning problem när ett stort antal steg körs samtidigt.

### <a name="azure-portal"></a>Azure Portal

+ **Nya funktioner**
  + Ny dra och släpp tabell redigeraren upplevelse för rapporter. Användare kan dra en kolumn från brunnen till området tabellen där en förhandsgranskning av tabellen visas. Kolumnerna som kan skrivas om.
  + Granskaren nya loggar
  + Länkar till experiment körs, beräkning, modeller, bilder och distributioner från fliken aktiviteter

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Förbered SDK v1.0.15

+ **Nya funktioner**
  + Data Prep stöder nu skriva filen dataströmmar från ett dataflöde. Ger också möjlighet att ändra stream filnamn för att skapa nya filnamn.
    + Här guiden: [Arbeta med filen strömmar notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Felkorrigeringar och förbättringar**
  + Förbättrad prestanda för t-Digest på stora datamängder.
  + Dataförberedelser har nu stöd för läsning av data från en DataPath.
  + En frekvent kodning fungerar nu på booleskt och numeriska kolumner.
  + Andra diverse felkorrigeringar.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning-SDK för Python v1.0.15

+ **Nya funktioner**
  + Azure Machine Learning Pipelines har lagts till AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (bärbar dator), och tidsbaserade schemaläggning funktioner ([notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep uppdateras för att arbeta med Azure SQL Server och Azure database för PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Ändringar**
  + Inaktuell `PublishedPipeline.get_published_pipeline` för `PublishedPipeline.get`.
  + Inaktuell `Schedule.get_schedule` för `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nya funktioner**
  + Data Prep stöder nu läsa från en Azure SQL-databas med datalagret.
 
+ **Ändringar**
  + Förbättrade minnesprestanda för vissa åtgärder på stora mängder data.
  + `read_pandas_dataframe()` kräver nu att `temp_folder` anges.
  + Den `name` egenskapen `ColumnProfile` har föråldrat – Använd `column_name` i stället.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning-SDK för Python v1.0.10

+ **Ändringar**: 
  + Azure ML-SDK har inte längre azure cli-paket som beroende. Mer specifikt har beroenden för azure-cli-core och azure-cli-profil tagits bort från azureml-kärna. Det här är den användare som påverkas av ändringar:
    + Om du utför ”az login” och sedan använda azureml-sdk, gör SDK: N koden loggen webbläsare eller enhet i en gång till. Den använder inte några autentiseringsuppgifter tillstånd som skapats av ”az login”.
    + Azure CLI-autentisering, till exempel med hjälp av ”az login” använda _azureml.core.authentication.AzureCliAuthentication_ klass. Azure CLI-autentisering, göra _pip installerar azure cli-_ i Python-miljön där du har installerat azureml-sdk.
    + Om du gör ”az login” med ett huvudnamn för tjänsten för automation, bör du använda _azureml.core.authentication.ServicePrincipalAuthentication_ klassen som azureml-sdk inte använder autentiseringsuppgifter tillstånd som skapats av azure CLI. 

+ **Felkorrigeringar**: Den här versionen innehåller främst mindre felkorrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Förbered SDK v1.0.8

+ **Felkorrigeringar**
  + Förbättrad prestanda för att få data profiler.
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
  + Förbättringar av datalager (beskrivs i [How-to-till-guide-datalager](https://aka.ms/aml-data-prep-datastore-nb))
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
