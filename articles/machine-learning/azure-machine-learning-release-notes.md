---
title: Vad är nytt i utgåvan?
titleSuffix: Azure Machine Learning
description: Lär dig mer om de senaste uppdateringarna av Azure Machine Learning och machine learning och data prep Python SDK:er.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 2739e960c4bc6d35ac9ad757e80ed620e5dc04f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684648"
---
# <a name="azure-machine-learning-release-notes"></a>Viktig information om Azure Machine Learning

I den här artikeln kan du läsa mer om Azure Machine Learning-versioner.  Det fullständiga SDK-referensinnehållet finns på Azure Machine Learnings [**huvudreferenssida SDK för Python.**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Se [listan över kända problem](resource-known-issues.md) för att lära dig mer om kända buggar och lösningar.

## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK för Python v1.3.0

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Lade till ytterligare telemetri kring efterutbildningen.
    + Snabbar upp automatisk ARIMA-utbildning genom att använda villkorlig summa kvadrater (CSS) utbildning för serie av längd längre än 100. Observera att längden som används lagras som konstant ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/i klassen TimeSeriesInternal på /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + Användarloggningen av prognoskörningar förbättrades, nu mer information om vilken fas som för närvarande körs visas i loggen
    + Otillåtna target_rolling_window_size som ska anges till värden mindre än 2
  + **azureml-automl-runtime**
    + Förbättrade felmeddelandet som visas när dubblerade tidsstämplar hittas.
    + Otillåtna target_rolling_window_size som ska anges till värden mindre än 2.
    + Fixade fördröjningsavräkningsfelet. Problemet orsakades av det otillräckliga antalet observationer som krävs för att säsongsvis sönderdelas en serie. "De-seasonalized" data används för att beräkna en partiell autokorrelation funktion (PACF) för att bestämma fördröjningen längd.
    + Aktiverad kolumn syfte featurization anpassning för prognoser uppgifter genom featurization config. Numeriskt och kategoriskt som kolumnsyfte för prognostisering av aktiviteter stöds nu.
    + Aktiverad släppkolumn med anpassning för prognostisering av aktiviteter genom featurization config.
    + Aktiverad avräkningsanpassning för prognostisering av aktiviteter genom featurization config. Konstant värdeimputering för målkolumn och medelvärde, median, most_frequent och konstant värdeimputering för träningsdata stöds nu.
  + **azureml-contrib-pipeline-steps**
    + Acceptera strängberäkningsnamn som ska skickas till ParallelRunConfig
  + **azureml-core**
    +  Api:et för att skapa en kopia av miljöobjektet har lagts till.clone(new_name) för att skapa en kopia av miljöobjektet
    +  Environment.docker.base_dockerfile accepterar filsökvägen. Om du kan lösa en fil läss innehållet in i base_dockerfile miljöegenskap
    + Återställ automatiskt ömsesidigt uteslutande värden för base_image och base_dockerfile när användaren manuellt anger ett värde i Environment.docker
    +  Datauppsättning: fast datauppsättningshämtningsfel om datasökvägen som innehåller unicode-tecken
    +  Datauppsättning: förbättrad cachelagringsmekanism för datauppsättning för att respektera det lägsta diskutrymmeskravet i Azure Machine Learning Compute, vilket undviker att göra noden oanvändbar och gör att jobbet avbryts
    + Lade till user_managed-flaggan i RSection som anger om miljön hanteras av användaren eller av AzureML.
    + Datauppsättning: vi lägger till ett index för timeseriekolumnen när du öppnar en tidseriedatauppsättning som en pandas dataframes, som används för att påskynda tillgången till timeseriebaserad dataåtkomst.  Tidigare fick indexet samma namn som tidsstämpelkolumnen, vilket förvirrade användare som är den faktiska tidsstämpelkolumnen och som är indexet. Vi ger nu inte något specifikt namn till indexet eftersom det inte bör användas som en kolumn. 
  + **azureml-dataprep**
    + Autentiseringsproblem med fast datauppsättning i suveränt moln
    + Åtgärdat `Dataset.to_spark_dataframe` fel för datauppsättningar som skapats från Azure PostgreSQL-datalager
  + **azureml-interpret**
    + Lade till globala poäng i visualisering om lokala betydelsevärden är glesa
    + Uppdaterad azureml-tolkning för att använda interpret-community 0.9.*
    + Fast problem med nedladdning förklaring som hade glesa utvärderingsdata
    + Lagt till stöd för glesformat för förklaringsobjektet i AutoML
  + **azureml-pipeline-core**
    + Stöd ComputeInstance som beräkningsmål i pipelines
  + **azureml-train-automl-client**
    + Lade till ytterligare telemetri kring efterutbildningen.
    + Fixade regressionen i tidigt stopp
    + Inaktuell azureml.dprep.Dataflow som en giltig typ för indata.
    +  Ändra standardtidsgränsen för AutoML-experiment till 6 dagar.
  + **azureml-train-automl-runtime**
    + Lade till ytterligare telemetri kring efterutbildningen.
    + lagt till glesa automl e2e-stöd
  + **azureml-opendatasets**
    + Lade till ytterligare telemetri för serviceövervakaren.
    + Aktivera frontdoor för blob för att öka stabiliteten 
## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK för Python v1.2.0

+ **Icke-bakåtkompatibla ändringar**
  + Släpp stöd för python 2.7

+ **Buggfixar och förbättringar**
  + **azurblå-cli-ml**
    + Lägger till "--subscription-id" i `az ml model/computetarget/service` kommandon i CLI
    + Lägga till stöd för att skicka kundhanterad key(CMK) vault_url, key_name och key_version för ACI-distribution
  + **azureml-automl-core** 
    + Aktiverad anpassad imputation med konstant värde för både X- och y-dataprognosaktiviteter.
    + Fixade problemet med att visa felmeddelanden för användaren.    
  + **azureml-automl-runtime**
    + Fixade problemet med prognoser på datauppsättningarna, som innehåller korn med endast en rad
    + Minskade mängden minne som krävs av prognosaktiviteterna.
    + Lade till bättre felmeddelanden om tidskolumnen har felaktigt format.
    + Aktiverad anpassad imputation med konstant värde för både X- och y-dataprognosaktiviteter.
  + **azureml-core**
    + Lägg till stöd för inläsning av ServicePrincipal från miljövariabler: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID och AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introducerade en ny `support_multi_line` `Dataset.Tabular.from_delimited_files`parameter till`support_multi_line=False`: Som standard ( ), kommer alla radbrytningar, inklusive de i citerade fältvärden, att tolkas som ett postavbrott. Att läsa data på det här sättet är snabbare och mer optimerat för parallell körning på flera CPU-kärnor. Det kan dock leda till att fler poster med feljusterade fältvärden i tysthet produceras. Detta bör ställas `True` in på när de avgränsade filerna är kända för att innehålla citerade radbrytningar.
    + Lade till möjligheten att registrera ADLS Gen2 i Azure Machine Learning CLI
    + Omdöpt parametern "fine_grain_timestamp" till "tidsstämpel" och parametern "coarse_grain_timestamp" till "partition_timestamp" för metoden with_timestamp_columns() i TabularDataset för att bättre återspegla användningen av parametrarna.
    + Ökad max experimentnamnlängd till 255.
  + **azureml-interpret**
    + Uppdaterad azureml-tolkning till interpret-community 0.7.*
  + **azureml-sdk**
    + Ändra till beroenden med kompatibel version Tilde för stöd för korrigering i pre-release och stabila versioner.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK för Python v1.1.5

+ **Utfasning av funktionen**
  + **Python 2.7**
    + Senaste versionen för att stödja python 2.7

+ **Icke-bakåtkompatibla ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1.1 Azure ML Python SDK antar semantisk versionshantering 2.0.0. [Läs mer här](https://semver.org/) Alla efterföljande versioner kommer att följa nya numreringsschema och semantiska versionshanteringskontrakt. 

+ **Buggfixar och förbättringar**
  + **azurblå-cli-ml**
    + Ändra kommandotonamnet för slutpunkten CLI från "az ml endpoint aks" till 'az ml endpoint realtime' för konsekvens.
    + uppdatera CLI-installationsinstruktioner för stabil och experimentell gren CLI
    + En instans profilering fastställdes för att producera en rekommendation och gjordes tillgänglig i core sdk.
  + **azureml-automl-core**
    + Aktiverade inferensen för batchläge (med flera rader en gång) för onnx-modeller med automl
    + Förbättrad upptäckt av frekvens på datamängderna, saknade data eller innehåller oregelbundna datapunkter
    + Lade till möjligheten att ta bort datapunkter som inte uppfyller den dominerande frekvensen.
    + Ändrade indata för konstruktorn för att ta en lista med alternativ för att tillämpa imputeringsalternativen för motsvarande kolumner.
    + Felloggningen har förbättrats.
  + **azureml-automl-runtime**
    + Åtgärdade problemet med det fel som uppstod om det korn som inte fanns i träningsuppsättningen dök upp i testuppsättningen
    + Tog bort y_query-kravet under bedömning av prognostjänst
    + Fixade problemet med prognoser när datauppsättningen innehåller korta korn med långa tidsluckor.
    + Åtgärdade problemet när den automatiska maxhorisonten är aktiverad och datumkolumnen innehåller datum i form av strängar. Korrekt konvertering och felmeddelanden har lagts till för när konverteringen hittills inte är möjlig
    + Använda inbyggda NumPy och SciPy för serialisering och deserialisering mellanliggande data för FileCacheStore (används för lokala AutoML-körningar)
    + Fixade ett fel där misslyckade underordnade körningar kunde fastna i körläge.
    + Ökad hastighet av featurization.
    + Fixade frekvenskontrollen under poängsättningen, nu kräver prognosuppgifterna inte strikt frekvensjämnhet mellan tåg och testuppsättning.
    + Ändrade indata för konstruktorn för att ta en lista med alternativ för att tillämpa imputeringsalternativen för motsvarande kolumner.
    + Fasta fel relaterade till lagtypval.
    + Fixade det oklassificerade felet som uppstod på datauppsättningarna och hade korn med den enda raden
    + Fixade problemet med frekvensdetekteringssnöjlighet.
    + Åtgärdar en fel i AutoML-undantagshantering som orsakade den verkliga orsaken till att utbildningsfel ersattes av en AttributeError.
  + **azureml-cli-common azureml-cli-common azureml-cli-common azureml**
    + En instans profilering fastställdes för att producera en rekommendation och gjordes tillgänglig i core sdk.
  + **azureml-contrib-mir**
    + Lägger till funktioner i klassen MirWebservice för att hämta åtkomsttoken
    + Använd tokenauth för MirWebservice som standard under MirWebservice.run() anrop - Uppdatera endast om samtalet misslyckas
    + Mir webservice distribution kräver nu korrekt Skus [Standard_DS2_v2, Standard_F16, Standard_A2_v2] i stället för [Ds2v2, A2v2 och F16] respektive.
  + **azureml-contrib-pipeline-steps**
    + Valfri parameter side_inputs har lagts till i ParallelRunStep. Den här parametern kan användas för att montera mappen på behållaren. För närvarande stöds typer är DataReference och PipelineData.
    + Parametrar som skickas i ParallelRunConfig kan skrivas över genom att skicka pipelineparametrar nu. Nya pipelineparametrar som stöds aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count och aml_process_count_per_node är redan en del av tidigare utgåvan).
  + **azureml-core**
    + Distribuerade AzureML-webbtjänster kommer `INFO` nu som standard att logga. Detta kan styras `AZUREML_LOG_LEVEL` genom att ange miljövariabeln i den distribuerade tjänsten.
    + Python sdk använder identifieringstjänsten för att använda "api"-slutpunkten i stället för "pipelines".
    + Byt till de nya rutterna i alla SDK-samtal
    + Ändrar routning av anrop till ModelManagementService till en ny enhetlig struktur
      + Gjorde uppdateringsmetoden för arbetsytan allmänt tillgänglig.
      + Lade till image_build_compute parameter i arbetsytan uppdateringsmetod för att tillåta användare att uppdatera beräkningen för avbildningsversion
    +  Lade till utfasningsmeddelanden i det gamla profilarbetsflödet. Fast profilering cpu och minnesgränser
    + Lade till RSection som en del av Miljö för att köra R-jobb
    +  Lade till `Dataset.mount` validering för att skapa fel när datauppsättningens källa inte är tillgänglig eller inte innehåller några data.
    + Tillagd `--grant-workspace-msi-access` som en ytterligare parameter för Datastore CLI för registrering av Azure Blob Container, vilket gör att du kan registrera Blob Container som ligger bakom ett virtuella nätverk
    + En instans profilering fastställdes för att producera en rekommendation och gjordes tillgänglig i core sdk.
    + Åtgärdade problemet i aks.py _deploy
    + Validerar integriteten för modeller som överförs för att undvika tysta lagringsfel.
    + Användaren kan nu ange ett värde för auth-nyckeln när nycklar för webbtjänster återskapas.
    + Fast fel fel där versaler inte kan användas som datauppsättningens indatanamn
  + **azureml-defaults**
    + `azureml-dataprep`kommer nu att installeras `azureml-defaults`som en del av . Det krävs inte längre att installera dataprep[säkring] manuellt på beräkningsmål för att montera datauppsättningar.
  + **azureml-interpret**
    + Uppdaterad azureml-tolkning till interpret-community 0.6.*
    + Uppdaterad azureml-tolkning beror på tolkningscommunity 0.5.0
    + Lade till azureml-stil undantag till azureml-tolka
    + Fast DeepScoringExplainer serialisering för keras modeller
  + **azureml-mlflow**
    + Lägg till stöd för suveräna moln till azureml.mlflow
  + **azureml-pipeline-core**
    + Anteckningsbok för poängsättning av pipeline-batch använder nu ParallelRunStep
    + Fixade ett fel där PythonScriptStep-resultat felaktigt kunde återanvändas trots att argumentlistan ändras
    + Lade till möjligheten att ange kolumners typ när du anropade parse_*-metoderna på`PipelineOutputFileDataset`
  + **azureml-pipeline-steg**
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps` paketet. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.
    + Lagt till dokumentationsexempel för datauppsättning som PythonScriptStep-indata
  + **azureml-tensorboard**
    + uppdaterad azureml-tensorboard för att stödja tensorflow 2.0
    + Visa rätt portnummer när du använder en anpassad Tensorboard-port på en beräkningsinstans
  + **azureml-train-automl-client**
    + Åtgärdade ett problem där vissa paket kan installeras vid felaktiga versioner på fjärrkörningar.
    + fast FeaturizationConfig övergripande problem som filtrerar anpassade featurization config.
  + **azureml-train-automl-runtime**
    + Åtgärdat problemet med frekvensidentifiering i fjärrkörningarna
    + Flyttade `AutoMLStep` in `azureml-pipeline-steps` i paketet. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Stöd PyTorch version 1.4 i PyTorch Estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK för Python v1.1.2rc0 (förutgåva)

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Aktiverade inferensen för batchläge (med flera rader en gång) för onnx-modeller med automl
    + Förbättrad upptäckt av frekvens på datamängderna, saknade data eller innehåller oregelbundna datapunkter
    + Lade till möjligheten att ta bort datapunkter som inte överensstämmer med den dominerande frequrncy.
  + **azureml-automl-runtime**
    + Åtgärdade problemet med det fel som uppstod om det korn som inte fanns i träningsuppsättningen dök upp i testuppsättningen
    + Tog bort y_query-kravet under bedömning av prognostjänst
  + **azureml-contrib-mir**
    + Lägger till funktioner i klassen MirWebservice för att hämta åtkomsttoken
  + **azureml-core**
    + Distribuerade AzureML-webbtjänster kommer `INFO` nu som standard att logga. Detta kan styras `AZUREML_LOG_LEVEL` genom att ange miljövariabeln i den distribuerade tjänsten.
    + Åtgärda iterering på `Dataset.get_all` för att returnera alla datauppsättningar som registrerats med arbetsytan.
    + Förbättra felmeddelandet när ogiltig `path` typ skickas till argumentet att API:er för att skapa datauppsättningar.
    + Python sdk använder identifieringstjänsten för att använda "api"-slutpunkten i stället för "pipelines".
    + Byt till de nya rutterna i alla SDK-samtal
    + Ändrar routning av anrop till ModelManagementService till en ny enhetlig struktur
      + Gjorde uppdateringsmetoden för arbetsytan allmänt tillgänglig.
      + Lade till image_build_compute parameter i arbetsytan uppdateringsmetod för att tillåta användare att uppdatera beräkningen för avbildningsversion
    +  Lade till utfasningsmeddelanden i det gamla profilarbetsflödet. Fast profilering cpu och minnesgränser
  + **azureml-interpret**
    + uppdatera azureml-interpret till interpret-community 0.6.*
  + **azureml-mlflow**
    + Lägg till stöd för suveräna moln till azureml.mlflow
  + **azureml-pipeline-steg**
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package`. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.
  + **azureml-train-automl-client**
    + Åtgärdade ett problem där vissa paket kan installeras vid felaktiga versioner på fjärrkörningar.
  + **azureml-train-automl-runtime**
    + Åtgärdat problemet med frekvensidentifiering i fjärrkörningarna
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package`. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package`. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK för Python v1.1.1rc0 (förutgåva)

+ **Buggfixar och förbättringar**
  + **azurblå-cli-ml**
    + En instans profilering fastställdes för att producera en rekommendation och gjordes tillgänglig i core sdk.
  + **azureml-automl-core**
    + Felloggningen har förbättrats.
  + **azureml-automl-runtime**
    + Fixade problemet med prognoser när datauppsättningen innehåller korta korn med långa tidsluckor.
    + Åtgärdade problemet när den automatiska maxhorisonten är aktiverad och datumkolumnen innehåller datum i form av strängar. Vi har lagt till korrekt konvertering och förnuftigt fel om konvertering hittills inte är möjlig
    + Använda inbyggda NumPy och SciPy för serialisering och deserialisering mellanliggande data för FileCacheStore (används för lokala AutoML-körningar)
    + Fixade ett fel där misslyckade underordnade körningar kunde fastna i körläge.
  + **azureml-cli-common azureml-cli-common azureml-cli-common azureml**
    + En instans profilering fastställdes för att producera en rekommendation och gjordes tillgänglig i core sdk.
  + **azureml-core**
    + Tillagd `--grant-workspace-msi-access` som en ytterligare parameter för Datastore CLI för registrering av Azure Blob Container, vilket gör att du kan registrera Blob Container som ligger bakom ett virtuella nätverk
    + En instans profilering fastställdes för att producera en rekommendation och gjordes tillgänglig i core sdk.
    + Åtgärdade problemet i aks.py _deploy
    + Validerar integriteten för modeller som överförs för att undvika tysta lagringsfel.
  + **azureml-interpret**
    + lade till azureml-stil undantag till azureml-tolka
    + fast DeepScoringExplainer serialisering för keras modeller
  + **azureml-pipeline-core**
    + Anteckningsbok för poängsättning av pipeline-batch använder nu ParallelRunStep
  + **azureml-pipeline-steg**
    + Flyttade `AutoMLStep` in `azureml-pipeline-steps` i paketet. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.
  + **azureml-contrib-pipeline-steps**
    + Valfri parameter side_inputs har lagts till i ParallelRunStep. Den här parametern kan användas för att montera mappen på behållaren. För närvarande stöds typer är DataReference och PipelineData.
  + **azureml-tensorboard**
    + uppdaterad azureml-tensorboard för att stödja tensorflow 2.0
  + **azureml-train-automl-client**
    + fast FeaturizationConfig övergripande problem som filtrerar anpassade featurization config.
  + **azureml-train-automl-runtime**
    + Flyttade `AutoMLStep` in `azureml-pipeline-steps` i paketet. Inaktuella inom `AutoMLStep` `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Stöd PyTorch version 1.4 i PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK för Python v1.1.0rc0 (förutgåva)

+ **Icke-bakåtkompatibla ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1.1 Azure ML Python SDK antar semantisk versionshantering 2.0.0. [Läs mer här](https://semver.org/) Alla efterföljande versioner kommer att följa nya numreringsschema och semantiska versionshanteringskontrakt. 
  
+ **Buggfixar och förbättringar**
  + **azureml-automl-runtime**
    + Ökad hastighet av featurization.
    + Fixade frekvenskontrollen under poängsättningen, nu i prognosuppgifterna kräver vi inte strikt frekvensjämnhet mellan tåg och testuppsättning.
  + **azureml-core**
    + Användaren kan nu ange ett värde för auth-nyckeln när nycklar för webbtjänster återskapas.
  + **azureml-interpret**
    + Uppdaterad azureml-tolkning beror på tolkningscommunity 0.5.0
  + **azureml-pipeline-core**
    + Fixade ett fel där PythonScriptStep-resultat felaktigt kunde återanvändas trots att argumentlistan ändras
  + **azureml-pipeline-steg**
    + Lagt till dokumentationsexempel för datauppsättning som PythonScriptStep-indata
  + **azureml-contrib-pipeline-steps**
    + Parametrar som skickas i ParallelRunConfig kan skrivas över genom att skicka pipelineparametrar nu. Nya pipelineparametrar som stöds aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count och aml_process_count_per_node är redan en del av tidigare utgåvan).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK för Python v1.0.85

+ **Nya funktioner**
  + **azureml-core**
    + Hämta den aktuella kärnanvändningen och kvotbegränsningen för AmlCompute-resurser i en viss arbetsyta och prenumeration
  
  + **azureml-contrib-pipeline-steps**
    + Gör det möjligt för användaren att skicka tabelldatauppsättning som mellanliggande resultat från föregående steg till parallellrunstep

+ **Buggfixar och förbättringar**
  + **azureml-automl-runtime**
    + Tog bort kravet på y_query kolumn i begäran till den distribuerade prognostjänsten. 
    + Den "y_query" togs bort från Dominick's Orange Juice notebook service request avsnitt.
    + Fixade felförebyggande prognoser på de distribuerade modellerna och fungerade på datauppsättningar med datumtidskolumner.
    + Lade till Matthews Korrelationskoefficient som klassificeringsmått, för både binär och multiklassklassificering.
  + **azureml-contrib-tolka**
    + Borttagna text explainers från azureml-contrib-tolka som text förklaring har flyttats till tolka-text repo som kommer att släppas snart.
  + **azureml-core**
    + Datauppsättning: användningar för fildatauppsättning beror inte längre på numpy och pandor som ska installeras i python env.
    + Ändrade LocalWebservice.wait_for_deployment() för att kontrollera status för den lokala Docker-behållaren innan du försöker pinga dess hälsoslutpunkt, vilket avsevärt minskar den tid det tar att rapportera en misslyckad distribution.
    + Fixade initieringen av en intern egenskap som används i LocalWebservice.reload() när tjänstobjektet skapas från en befintlig distribution med hjälp av konstruktorn LocalWebservice().
    + Redigerat felmeddelande för förtydligande.
    + Lade till en ny metod som kallas get_access_token() till AksWebservice som returnerar AksServiceAccessToken-objektet, som innehåller åtkomsttoken, uppdatera efter tidsstämpel, förfallodatum på tidsstämpel och tokentyp. 
    + Inaktuell befintlig get_token()-metoden i AksWebservice när den nya metoden returnerar all information som den här metoden returnerar.
    + Ändrad utdata från kommandot get-access-token för az ml-tjänsten. Bytt namn token för att komma åtToken och refreshBy för att uppdateraEfter. Lade till egenskaper för expiryOn och tokenType.
    + Fast get_active_runs
  + **azureml-explain-modell**
    + uppdaterad shap till 0.33.0 och tolka-community till 0,4.*
  + **azureml-interpret**
    + uppdaterad shap till 0.33.0 och tolka-community till 0,4.*
  + **azureml-train-automl-runtime**
    + Lade till Matthews Korrelationskoefficient som klassificeringsmått, för både binär och multiklassklassificering.
    + Inaktuell förprocessflagga från kod och ersatt med featurization -featurization är aktiverat som standard

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK för Python v1.0.83

+ **Nya funktioner**
  + Datauppsättning: Lägg till `on_error` `out_of_range_datetime` två `to_pandas_dataframe` alternativ och för att misslyckas `None`när data har felvärden i stället för att fylla dem med .
  + Arbetsyta: Lade `hbi_workspace` till flaggan för arbetsytor med känsliga data som möjliggör ytterligare kryptering och inaktiverar avancerad diagnostik på arbetsytor. Vi har också lagt till stöd för att ta med egna `cmk_keyvault` `resource_cmk_uri` nycklar för den associerade Cosmos DB-instansen genom att ange och parametrar när du skapar en arbetsyta, vilket skapar en Cosmos DB-instans i din prenumeration när du etablerar arbetsytan. [Läs mer här.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Buggfixar och förbättringar**
  + **azureml-automl-runtime**
    + Fixade en regression som gjorde att en TypeError höjs när AutoML kördes i Python-versioner under 3.5.4.
  + **azureml-core**
    + Fast bugg `datastore.upload_files` där relativ sökväg som `./` inte började med inte kunde användas.
    + Lade till meddelanden om utfasning för alla kodsöker i bildklass
    + Fast modellhantering URL konstruktion för Mooncake regionen.
    + Åtgärdat problem där modeller som använder source_dir inte kunde paketeras för Azure Functions.    
    + Lade till ett alternativ i [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) för att skicka en avbildning till AzureML-behållarens register
    + Uppdaterade SDK för att använda nytt tokenbibliotek på azure synapse på ett bakåtkompatibelt sätt.
  + **azureml-interpret**
    + Fast bugg där Ingen returnerades när inga förklaringar fanns tillgängliga för nedladdning. Nu väcker ett undantag, matchande beteende någon annanstans.
  + **azureml-pipeline-steg**
    + Otillåtna passningar `DatasetConsumptionConfig`till `Estimator`'s `inputs` parameter `Estimator` när kommer `EstimatorStep`att användas i en .
  + **azureml-sdk**
    + Lade till AutoML-klienten i azureml-sdk-paketet, vilket gör att fjärrstyrda AutoML-körningar skickas utan att installera hela AutoML-paketet.
  + **azureml-train-automl-client**
    + Korrigerad justering på konsolutgång för automl-körningar
    + Fixade ett fel där felaktig version av pandor kan installeras på fjärramlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK för Python v1.0.81

+ **Buggfixar och förbättringar**
  + **azureml-contrib-tolka**
    + skjuta upp shap beroende att tolka-community från azureml-tolka
  + **azureml-core**
    + Beräkningsmålet kan nu anges som en parameter till motsvarande distributionskonfigurationsobjekt. Detta är specifikt namnet på beräkningsmålet som ska distribueras till, inte SDK-objektet.
    + Lade till CreatedBy-information i modell- och tjänstobjekt. Kan nås via <var>.created_by
    + Fixed ContainerImage.run(), som inte korrekt konfigurerade Docker-behållarens HTTP-port.
    + Gör `azureml-dataprep` valfri `az ml dataset register` för cli-kommando
    + Fixade en `TabularDataset.to_pandas_dataframe` bugg där skulle felaktigt falla tillbaka till en alternativ läsare och skriva ut en varning.
  + **azureml-explain-modell**
    + skjuta upp shap beroende att tolka-community från azureml-tolka
  + **azureml-pipeline-core**
    + Lade till `NotebookRunnerStep`nytt pipeline-steg för att köra en lokal anteckningsbok som ett steg på gång.
    + Borttagna inaktuella get_all funktioner för PublishedPipelines, Schedules och PipelineEndpoints
  + **azureml-train-automl-client**
    + Startade utfasning av data_script som en indata till AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK för Python v1.0.79

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Borttagen featurizationConfig som ska loggas
      + Uppdaterad loggning för att logga "auto"/"off"/"anpassad" endast.
  + **azureml-automl-runtime**
    + Lagt till stöd för pandor. Serie och pandor. Kategorisk för identifiering av kolumndatatyp. Tidigare endast stöds numpy.ndarray
      + Lade till relaterade kodändringar för att hantera kategorisk dtype korrekt.
    + Prognosfunktionsgränssnittet förbättrades: parametern y_pred gjordes valfri. -Docstrings förbättrades.
  + **azureml-contrib-dataset**
    + Fixade ett fel där det inte gick att montera etiketterna datauppsättningar.
  + **azureml-core**
    + Buggfix `Environment.from_existing_conda_environment(name, conda_environment_name)`för . Användaren kan skapa en instans av miljö som är exakt replik av den lokala miljön
    + Ändrade tidsserierelaterade datauppsättningsmetoder `include_boundary=True` till som standard.
  + **azureml-train-automl-client**
    + Fast problem där valideringsresultat inte skrivs ut när visa utdata är inställt på false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK för Python v1.0.76

+ **Icke-bakåtkompatibla ändringar**
  + Uppgraderingsproblem för Azureml-Train-AutoML
    + Uppgradering till azureml-train-automl>=1.0.76 från azureml-train-automl<1.0.76 kan orsaka partiella installationer, vilket gör att vissa automl-import misslyckas. Lös problemet genom att köra installationsskriptet som finns på https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Eller om du använder pip direkt kan du:
      + "pip installera --uppgradera azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + eller så kan du avinstallera den gamla versionen innan du uppgraderar
      + "pip avinstallera azureml-train-automl"
      + "pip installera azureml-train-automl"

+ **Buggfixar och förbättringar**
  + **azureml-automl-runtime**
    + AutoML tar nu hänsyn till både sanna och falska klasser vid beräkning av genomsnittliga skalmått för binära klassificeringsuppgifter.
    + Flyttade machine learning and training code i AzureML-AutoML-Core till ett nytt paket AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + När `to_pandas_dataframe` du anropar en märkt datauppsättning med hämtningsalternativet kan du nu ange om du vill skriva över befintliga filer eller inte.
    + När `keep_columns` du `drop_columns` anropar eller som resulterar i att en tidsserie, etikett eller bildkolumn tas bort, kommer motsvarande funktioner att tas bort även för datauppsättningen.
    + Åtgärdade ett problem med pytorch loader för objektidentifieringsuppgiften.
  + **azureml-contrib-tolka**
    + Tog bort förklaringsinstrumentpanelswidget från azureml-contrib-interpret, ändrat paket för att referera till det nya i interpret_community
    + Uppdaterad version av interpret-community till 0.2.0
  + **azureml-core**
    + Förbättra prestanda `workspace.datasets`för .
    + Lade till möjligheten att registrera Azure SQL Database Datastore med hjälp av användarnamn och lösenordsautentisering
    + Åtgärdat för inläsning av RunConfigurations från relativa sökvägar.
    + När `keep_columns` du `drop_columns` ringer eller som resulterar i en timeserie kolumn tas bort, motsvarande funktioner kommer att tas bort för datauppsättningen också.
  + **azureml-interpret**
    + uppdaterad version av tolkningsgemenskap till 0.2.0
  + **azureml-pipeline-steg**
    + Dokumenterade värden som `runconfig_pipeline_params` stöds för azure machine learning pipeline-steg.
  + **azureml-pipeline-core**
    + Lade till CLI-alternativ för att hämta utdata i json-format för Pipeline-kommandon.
  + **azureml-train-automl**
    + Dela AzureML-Train-AutoML i 2 paket, ett klientpaket AzureML-Train-AutoML-Client och ett ML-utbildningspaket AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Lade till en tunn klient för att skicka AutoML-experiment utan att behöva installera några maskininlärningsberoenden lokalt.
    + Fast loggning av automatiskt upptäckta fördröjningar, rullande fönsterstorlekar och maximala horisonter i fjärrkörningarna.
  + **azureml-train-automl-runtime**
    + Lade till ett nytt AutoML-paket för att isolera maskininlärnings- och körningskomponenter från klienten.
  + **azureml-contrib-train-rl**
    + Lade till stöd för förstärkningsinlärning i SDK.
    + Lagt till AmlWindowsCompute-stöd i RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK för Python v1.0.74

  + **Förhandsgranska funktioner**
    + **azureml-contrib-dataset**
      + När du har importerat azureml-contrib-datauppsättning kan du anropa `Dataset.Labeled.from_json_lines` i stället `._Labeled` för att skapa en märkt datauppsättning.
      + När `to_pandas_dataframe` du anropar en märkt datauppsättning med hämtningsalternativet kan du nu ange om du vill skriva över befintliga filer eller inte.
      + När `keep_columns` du `drop_columns` anropar eller som resulterar i att en tidsserie, etikett eller bildkolumn tas bort, kommer motsvarande funktioner att tas bort även för datauppsättningen.
      + Åtgärdade problem med PyTorch-lastaren när du anropade `dataset.to_torchvision()`.

+ **Buggfixar och förbättringar**
  + **azurblå-cli-ml**
    + Lade till modellprofilering i förhandsversionen CLI.
    + Åtgärdar att bryta ändringen i Azure Storage, vilket gör att AzureML CLI misslyckas.
    + Lade till belastningsutjämnad typ till MLC för AKS-typer
  + **azureml-automl-core**
    + Fixade problemet med detektion av maximal horisont på tidsserier, med saknade värden och flera korn.
    + Åtgärdade problemet med fel under generering av korsvalideringsdelningar.
    + Ersätt det här avsnittet med ett meddelande i markdown-format för att visas i viktig information: -Förbättrad hantering av korta korn i prognosdatauppsättningarna.
    + Åtgärdade problemet med maskering av viss användarinformation vid loggning. -Förbättrad loggning av felen under prognoskörningar.
    + Lägga till psutil som ett conda-beroende till den automatiskt genererade yml-distributionsfilen.
  + **azureml-contrib-mir**
    + Åtgärdar att bryta ändringen i Azure Storage, vilket gör att AzureML CLI misslyckas.
  + **azureml-core**
    + Åtgärdar ett fel som orsakade att modeller som distribuerats på Azure Functions producerade 500-tal.
    + Åtgärdade ett problem där amlignorfilen inte tillämpades på ögonblicksbilder.
    + Lade till ett nytt API amlcompute.get_active_runs som returnerar en generator för att köra och köa körs på en viss amlcompute.
    + Lade till belastningsutjämnad typ till MLC för AKS-typer.
    + Lade till append_prefix bool parameter för att download_files i run.py och download_artifacts_from_prefix i artifacts_client. Den här flaggan används för att selektivt förenkla ursprungsfilsökvägen så att endast fil- eller mappnamnet läggs till i output_directory
    + Åtgärda avserialiseringsproblem `run_config.yml` för med datauppsättningsanvändning.
    + När `keep_columns` du `drop_columns` ringer eller som resulterar i att en tidsseriekolumn tas bort, kommer motsvarande funktioner att tas bort även för datauppsättningen.
  + **azureml-interpret**
    + Uppdaterad tolkningsversion till 0.1.0.3
  + **azureml-train-automl**
    + Åtgärdade ett problem där automl_step kanske inte skriver ut verifieringsproblem.
    + Fast register_model för att lyckas även om modellens miljö saknar beroenden lokalt.
    + Åtgärdade ett problem där vissa fjärrkörningar inte var dockeraktiverade.
    + Lägg till loggning av undantaget som gör att en lokal körning misslyckas i förtid.
  + **azureml-train-core**
    + Överväg resume_from körs i beräkningen av automatisk hyperparameter tuning bästa underordnade körningar.
  + **azureml-pipeline-core**
    + Fast parameterhantering i rörledningar argument konstruktion.
    + Lade till pipelinebeskrivning och stegtyp yaml-parameter.
    + Nytt yaml-format för pipeline-steg och lagt till varning för gamla format.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webbupplevelse

Den gemensamma målsidan [https://ml.azure.com](https://ml.azure.com) för arbetsytan på har förbättrats och bytt namn till Azure Machine Learning Studio (förhandsversion).

Från studion kan du träna, testa, distribuera och hantera Azure Machine Learning-resurser som datauppsättningar, pipelines, modeller, slutpunkter med mera.

Få tillgång till följande webbaserade redigeringsverktyg från studion:

| Webbaserat verktyg | Beskrivning | Utgåva |
|-|-|-|
| Dator-VM(förhandsgranskning) | Fullständigt hanterad molnbaserad arbetsstation | Grundläggande & Företag |
| [Automatiserad maskininlärning](tutorial-first-experiment-automated-ml.md) (förhandsgranskning) | Ingen kodupplevelse för att automatisera maskininlärningsmodellutveckling | Enterprise |
| [Designer](concept-designer.md) (förhandsgranskning) | Verktyg för maskininlärningsmodellering för drag och släpp tidigare känt som designern | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Förbättringar av Azure Machine Learning-designer

+ Tidigare känd som det visuella gränssnittet 
+    11 nya [moduler,](algorithm-module-reference/module-reference.md) inklusive rekommenderare, klassificerare och utbildningsverktyg, inklusive funktionsteknik, korsvalidering och dataomvandling.

### <a name="r-sdk"></a>R SDK 
 
Dataexperter och AI-utvecklare använder [Azure Machine Learning SDK för R för](tutorial-1st-r-experiment.md) att skapa och köra arbetsflöden för maskininlärning med Azure Machine Learning.

Azure Machine Learning SDK för `reticulate` R använder paketet för att binda till Python SDK. Genom att binda direkt till Python ger SDK för R dig tillgång till kärnobjekt och metoder som implementeras i Python SDK från vilken R-miljö du väljer.

Huvudfunktionerna i SDK är:

+    Hantera molnresurser för övervakning, loggning och organisering av Machine Learning-experiment.
+    Träna modeller med molnresurser, inklusive GPU-accelererad modellutbildning.
+    Distribuera dina modeller som webbtjänster på Azure Container Instances (ACI) och Azure Kubernetes Service (AKS).

Se [paketets webbplats](https://azure.github.io/azureml-sdk-for-r) för fullständig dokumentation.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning-integrering med Event Grid 

Azure Machine Learning är nu en resursleverantör för Event Grid, du kan konfigurera machine learning-händelser via Azure-portalen eller Azure CLI. Användare kan skapa händelser för körningskomplettering, modellregistrering, modelldistribution och datadrift som upptäckts. Dessa händelser kan dirigeras till händelsehanterare som stöds av Event Grid för förbrukning. Mer information finns i [händelseschema för](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)maskininlärning, [begrepp](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) och [självstudieartiklar.](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid)

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK för Python v1.0.72

+ **Nya funktioner**
  + Lade till datauppsättningsövervakare via [**azureml-datadrift-paketet,**](https://docs.microsoft.com/python/api/azureml-datadrift) vilket möjliggör övervakning av tidsseriedatauppsättningar för datadrift eller andra statistiska ändringar över tiden. Aviseringar och händelser kan utlösas om drift upptäcks eller andra villkor på data är uppfyllda. Se [vår dokumentation](https://aka.ms/datadrift) för mer information.
  + Tillkännage två nya utgåvor (kallas även en SKU omväxlande) i Azure Machine Learning. Med den här versionen kan du nu skapa en basic- eller Enterprise Azure Machine Learning-arbetsyta. Alla befintliga arbetsytor kommer att vara standard till Basic-utgåvan och du kan gå till Azure-portalen eller till studion för att uppgradera arbetsytan när som helst. Du kan skapa en Basic- eller Enterprise-arbetsyta från Azure-portalen. Läs gärna mer om hur vi bedriver [vårt material.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) Från SDK kan upplagan av arbetsytan bestämmas med hjälp av egenskapen "sku" för ditt arbetsyteobjekt.
  + Vi har också gjort förbättringar av Azure Machine Learning Compute - du kan nu visa mått för dina kluster (som totala noder, köra noder, total kärnkvot) i Azure Monitor, förutom att visa diagnostikloggar för felsökning. Dessutom kan du också visa körs eller köa körs på klustret och detaljer som IPs för de olika noderna i klustret. Du kan visa dessa antingen i portalen eller genom att använda motsvarande funktioner i SDK eller CLI.

  + **Förhandsgranska funktioner**
    + Vi släpper förhandsgranskningsstöd för diskkryptering av din lokala SSD i Azure Machine Learning Compute. Höj en teknisk supportbiljett för att få din prenumeration vitlistad för att kunna använda den här funktionen.
    + Offentlig förhandsversion av Azure Machine Learning Batch Inferens. Azure Machine Learning Batch Inference riktar sig till stora inferensjobb som inte är tidskänsliga. Batch Inference ger kostnadseffektiv inferens beräkningskalning, med enastående dataflöde för asynkrona program. Den är optimerad för hög genomströmning, brand-och-glömma inferens över stora samlingar av data.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Aktiverade funktioner för märkt datauppsättning
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Buggfixar och förbättringar**
  + **azurblå-cli-ml**
    + CLI stöder nu modellförpackningar.
    + Lade till datauppsättning CLI. För mer information:`az ml dataset --help`
    + Lade till stöd för att distribuera och paketera modeller som stöds (ONNX, scikit-learn och TensorFlow) utan en InferenceConfig-instans.
    + Lade till överskrivningsflagga för tjänstdistribution (ACI och AKS) i SDK och CLI. Om det tillhandahålls, kommer att skriva över den befintliga tjänsten om tjänsten med namn redan finns. Om tjänsten inte finns, kommer att skapa en ny tjänst.
    + Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. - Modellregistrering accepterar exempelindata, exempeldata och resurskonfiguration för modellen.
  + **azureml-automl-core**
    + Utbildning en iteration skulle köras i en underordnad process endast när körningsbegränsningar ställs in.
    + Lade till en skyddsräcke för prognosuppgifter för att kontrollera om en angiven max_horizon kommer att orsaka ett minnesproblem på den angivna datorn eller inte. Om så kommer det att visas ett skyddsräcke.
    + Lagt till stöd för komplexa frekvenser som 2 år och 1 månad. -Lagt till begripligt felmeddelande om frekvensen inte kan fastställas.
    + Lägg till azureml-standardinställningar för automatiskt genererad conda env för att lösa modelldistributionsfelet
    + Tillåt att mellanliggande data i Azure Machine Learning Pipeline konverteras `AutoMLStep`till tabelldatauppsättning och används i .
    + Implementerad kolumn syfte uppdatering för streaming.
    + Implementerad transformatorparameteruppdatering för Imputer och HashOneHotEncoder för direktuppspelning.
    + Lade till den aktuella datastorleken och den minsta datastorlek som krävs i valideringsfelmeddelandena.
    + Uppdaterade den minsta datastorlek som krävs för korsvalidering för att garantera minst två samplingar i varje valideringsvecka.
  + **azureml-cli-common azureml-cli-common azureml-cli-common azureml**
    + CLI stöder nu modellförpackningar.
    + Modeller kan registreras med två nya ramverk, Onnx och Tensorflow.
    + Modellregistrering accepterar exempelindata, exempeldata och resurskonfiguration för modellen.
  + **azureml-contrib-gbdt**
    + fixade utlösningskanalen för den bärbara datorn
    + Lade till en varning för beräkningsmål som inte är AmlCompute som vi inte stöder
    + Lade till LightGMB-estimator i azureml-contrib-gbdt-paketet
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI stöder nu modellförpackningar.
    + Lägg till varning för utfasning för inaktuella api:er för datauppsättning. Se meddelande om ändring https://aka.ms/tabular-datasetav datauppsättnings-API på .
    + Ändra [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) för att returnera registreringsnamn och version om datauppsättningen är registrerad.
    + Åtgärda ett fel som ScriptRunConfig med datauppsättning som argument inte kan användas upprepade gånger för att skicka experimentkörning.
    + Datauppsättningar som hämtas under en körning spåras och kan ses [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) på sidan körinformation eller genom att anropa när körningen är klar.
    + Tillåt att mellanliggande data i Azure Machine Learning Pipeline konverteras [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)till tabelldatauppsättning och används i .
    + Lade till stöd för att distribuera och paketera modeller som stöds (ONNX, scikit-learn och TensorFlow) utan en InferenceConfig-instans.
    + Lade till överskrivningsflagga för tjänstdistribution (ACI och AKS) i SDK och CLI. Om det tillhandahålls, kommer att skriva över den befintliga tjänsten om tjänsten med namn redan finns. Om tjänsten inte finns, kommer att skapa en ny tjänst.
    +  Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. Modellregistrering accepterar exempelindata, exempeldata och resurskonfiguration för modellen.
    + Lade till nytt datalager för Azure Database för MySQL. Lade till exempel för att använda Azure Database för MySQL i DataTransferStep i Azure Machine Learning Pipelines.
    + Lade till funktioner för att lägga till och ta bort taggar från experiment Added-funktioner för att ta bort taggar från körningar
    + Lade till överskrivningsflagga för tjänstdistribution (ACI och AKS) i SDK och CLI. Om det tillhandahålls, kommer att skriva över den befintliga tjänsten om tjänsten med namn redan finns. Om tjänsten inte finns, kommer att skapa en ny tjänst.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Flyttade `azureml-contrib-datadrift` från till`azureml-datadrift`
    + Extra stöd för övervakning av tidsseriedatauppsättningar för drift och andra statistiska åtgärder
    + Nya `create_from_model()` metoder `create_from_dataset()` och [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) till klassen. Metoden `create()` kommer att vara inaktuell.
    + Justeringar av visualiseringar i Python och användargränssnittet i Azure Machine Learning-studion.
    + Stöd veckovis och månadsvis övervaka schemaläggning, förutom dagligen för datauppsättningsövervakare.
    + Stöd för återfyllning av dataövervakare för att analysera historiska data för datauppsättningsövervakare.
    + Olika buggfixar
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep behövs inte längre för att skicka en `yaml` Azure Machine Learning Pipeline-körning från pipeline-filen.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Lägg till azureml-standardinställningar för automatiskt genererad conda env för att lösa modelldistributionsfelet
    + Automatisk fjärrträning innehåller nu azureml-defaults för att tillåta återanvändning av träningsenv för inferens.
  + **azureml-train-core**
    + La PyTorch 1,3 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) stöd i estimator

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuellt gränssnitt (förhandsgranskning)

+ Det visuella gränssnittet för Azure Machine Learning (förhandsversion) har setts över för att köras på [Azure Machine Learning-pipelines](concept-ml-pipelines.md). Pipelines (tidigare kända experiment) som skapats i det visuella gränssnittet är nu helt integrerade med azure Machine Learning-kärnupplevelsen.
  + Enhetlig hanteringserfarenhet med SDK-tillgångar
  + Versionshantering och spårning för visuella gränssnittsmodeller, rörledningar och slutpunkter
  + Omdesignat användargränssnitt
  + Distribution av batchupptråhet
  + La Azure Kubernetes Service (AKS) stöd för inferens beräkningsmål
  + Nytt arbetsflöde för redigering av Python-steg
  + Ny [målsida](https://ml.azure.com) för verktyg för visuell redigering

+ **Nya moduler**
  + Använda matematisk åtgärd
  + Använda SQL-omvandling
  + Klipp värden
  + Summera data
  + Importera från SQL-databas

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK för Python v1.0.69

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Begränsa modellförklaringar till bästa körning snarare än att beräkna förklaringar för varje körning. Att göra detta beteende förändras för lokala, avlägsna och ADB.
    + Lagt till stöd för modellförklaringar på begäran för användargränssnitt
    + Lade psutil som ett `automl` beroende av och ingår psutil som en conda beroende i amlcompute.
    + Fixade problemet med heuristiska fördröjningar och rullande fönsterstorlekar på prognosdatauppsättningarna vissa serier som kan orsaka linjära algebrafel
      + Lade till utskrift för de heuristiskt bestämda parametrarna i prognoskörningarna.
  + **azureml-contrib-datadrift**
    + Lade till skydd när du skapar utdatamått om avdrift på datamängdsnivå inte finns i det första avsnittet.
  + **azureml-contrib-tolka**
    + azureml-contrib-explain-model-paketet har bytt namn till azureml-contrib-interpret
  + **azureml-core**
    + Lade till API för att avregistrera datauppsättningar. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model-paketet har bytt namn till azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Lade till API för att avregistrera datauppsättningar. Datamängd. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Lade till Dataset API för att kontrollera data ändrade tid. `dataset.data_changed_time`.
    + Att kunna `FileDataset` använda `TabularDataset` och som `PythonScriptStep` `EstimatorStep`indata `HyperDriveStep` till , och i Azure Machine Learning Pipeline
    + Prestanda `FileDataset.mount` för har förbättrats för mappar med ett stort antal filer
    + Att kunna använda [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) och [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) som indata till [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)och [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) i Azure Machine Learning Pipeline.
    + Prestanda för FileDataset. [mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) har förbättrats för mappar med ett stort antal filer
    + Lade till URL till kända felrekommendationer i körningsinformation.
    + Fixade ett fel i run.get_metrics där begäranden skulle misslyckas om en körning hade för många barn
    + Fixade ett fel i [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) där begäranden skulle misslyckas om en körning hade för många barn
    + Lade till stöd för autentisering i Arcadia-kluster.
    + När du skapar ett experimentobjekt får eller skapas experimentet på arbetsytan Azure Machine Learning för spårning av körningshistorik. Experiment-ID och arkiverad tid fylls i experimentobjektet när det skapas. Exempel: experiment = Experiment(arbetsyta, "Nytt experiment") experiment_id = experiment.id archive() och reactivate() är funktioner som kan anropas på ett experiment för att dölja och återställa experimentet från att visas i användarupplevelsen eller returneras som standard i ett anrop till listexperiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett förnamn. Exempel: experiment1 = Experiment(arbetsyta, "Aktivt experiment") experiment1.archive() # Skapa nytt aktivt experiment med samma namn som det arkiverade. experiment2. = Experiment(arbetsyta, experiment med aktivt experiment1.reactivate(new_name="Föregående aktivt experiment") Den statiska metodlistan() på Experiment kan ta ett namnfilter och ViewType-filtret. ViewType-värdena är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "ALLA" Exempel: archived_experiments = Experiment.list(arbetsyta, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(arbetsyta, namn="Första experiment", view_type="ALLA")
    + Stöd med miljö för modellut distribuera och serviceuppdatering
  + **azureml-datadrift**
    + Visa-attributet för klassen DataDriftDector stöder inte längre det valfria argumentet "with_details". Visa-attributet kommer bara att presentera datadriftkoefficient och data drift bidrag av funktionen kolumner.
    + DataDriftDetector-attributet "get_output" ändras:
      + Indataparameter start_time, end_time är valfria i stället för obligatoriskt.
      + Indataspecifik start_time och/eller end_time med en specifik run_id i samma åberopande kommer att resultera i undantag för värdefel eftersom de utesluter värdefel
      + Genom att ange specifika start_time och/eller end_time returneras endast resultat av schemalagda körningar.
      + Parametern "daily_latest_only" är inaktuell.
    + Stöd för att hämta datauppsättningsbaserade datadriftutdata.
  + **azureml-explain-modell**
    + Byter namn på AzureML-explain-model-paket till AzureML-tolkning, vilket håller det gamla paketet för bakåtkompatibilitet för tillfället
    + fast `automl` bugg med råa förklaringar inställd på klassificeringsuppgift i stället för regression som standard vid nedladdning från ExplanationClient
    + Lägg till `ScoringExplainer` stöd för att skapas direkt med`MimicWrapper`
  + **azureml-pipeline-core**
    + Förbättrad prestanda för stora pipeline-skapande
  + **azureml-train-core**
    + Lagt till TensorFlow 2.0-stöd i TensorFlow Estimator
  + **azureml-train-automl**
    + När du skapar ett [experimentobjekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) får eller skapas experimentet på arbetsytan Azure Machine Learning för spårning av körningshistorik. Experiment-ID och arkiverad tid fylls i experimentobjektet när det skapas. Exempel:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) och [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) är funktioner som kan anropas på ett experiment för att dölja och återställa experimentet från att visas i användarupplevelsen eller returneras som standard i ett anrop till listexperiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett förnamn. Exempel:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Den statiska [metodlistan()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) på Experiment kan ta ett namnfilter och ViewType-filter. ViewType-värdena är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "ALL". Exempel:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Stöd med hjälp av miljö för modellut distribuera och serviceuppdatering.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Visa-attributet för klassen [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) stöder inte längre det valfria argumentet "with_details". Visa-attributet kommer bara att presentera datadriftkoefficient och data drift bidrag av funktionen kolumner.
    + DataDriftDetector-funktionen [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) beteendeförändringar:
      + Indataparameter start_time, end_time är valfria i stället för obligatoriskt.
      + Indataspecifika start_time och/eller end_time med en specifik run_id i samma åberopande kommer att resultera i undantag för värdefel eftersom de utesluter varandra.
      + Genom att ange specifika start_time och/eller end_time returneras endast resultat av schemalagda körningar.
      + Parametern "daily_latest_only" är inaktuell.
    + Stöd för att hämta datauppsättningsbaserade datadriftutdata.
  + **azureml-explain-modell**
    + Byter namn på AzureML-explain-model-paket till AzureML-tolkning, vilket behåller det gamla paketet för bakåtkompatibilitet för tillfället.
    + fast AutoML bugg med råa förklaringar inställd på klassificering uppgift i stället för regression som standard vid nedladdning från ExplanationClient.
    + Lägg till stöd för [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) som ska skapas direkt med MimicWrapper
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Förbättrad prestanda för stora Pipeline-skapande.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Lade till TensorFlow 2.0-stöd i [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Den överordnade körningen kommer inte längre att misslyckas när installationen iteration misslyckades, eftersom orkestrering redan tar hand om det.
    + Lagt till stöd för lokal docker och lokalt konfigurerat för AutoML-experiment
    + Lade till stöd för lokal docker och lokalt konfigurerat för AutoML-experiment.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webbupplevelse (förhandsversion) för Azure Machine Learning-arbetsytor

Fliken Experiment i den [nya arbetsytan](https://ml.azure.com) har uppdaterats så att dataexperter kan övervaka experiment på ett mer högpresterande sätt. Du kan utforska följande funktioner:
+ Experimentera metadata för att enkelt filtrera och sortera listan över experiment
+ Förenklade och utförliga experimentdetaljer sidor som gör att du kan visualisera och jämföra dina körningar
+ Ny design för att köra informationssidor för att förstå och övervaka dina träningskörningar

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK för Python v1.0.65

  + **Nya funktioner**
    + Lade till kurerade miljöer. Dessa miljöer har förkonfigurerats med bibliotek för vanliga maskininlärningsuppgifter och har förbyggts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard i Workspace lista över miljö, med prefixet "AzureML".
    + Lade till kurerade miljöer. Dessa miljöer har förkonfigurerats med bibliotek för vanliga maskininlärningsuppgifter och har förbyggts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard i [Workspace's](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)lista över miljö, med prefixet "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Lade till ONNX-konverteringsstödet för ADB och HDI

+ **Förhandsgranska funktioner**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT och BiLSTM som text med stöd (endast förhandsgranskning)
    + Anpassning med stöd för kolumnändamål och transformatorparametrar (endast förhandsgranskning)
    + Råa förklaringar som stöds när användaren aktiverar modellförklaring under träningen (endast förhandsgranskning)
    + Lade till `timeseries` prophet för prognoser som en tågbar pipeline (endast förhandsgranska)

  + **azureml-contrib-datadrift**
    + Paket som flyttas från azureml-contrib-datadrift till azureml-datadrift; paketet `contrib` kommer att tas bort i en framtida version

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Introducerade FeaturizationConfig till AutoMLConfig och AutoMLBaseSettings
    + Introducerade FeaturizationConfig till [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) och AutoMLBaseSettings
      + Åsidosätt kolumnsyfte för featurization med given kolumn och funktionstyp
      + Åsidosätta transformatorparametrar
    + Lade till meddelande om utfasning för explain_model() och retrieve_model_explanations()
    + Lade till prophet som en tågbar pipeline (endast förhandsgranskning)
    + Lade till meddelande om utfasning för explain_model() och retrieve_model_explanations().
    + Lade till Prophet som en tågbar pipeline (endast förhandsgranskning).
    + Lagt till stöd för automatisk detektering av målfördröjningar, rullande fönsterstorlek och maximal horisont. Om en av target_lags, target_rolling_window_size eller max_horizon är inställd på "auto", kommer heuristiken att tillämpas för att uppskatta värdet av motsvarande parameter baserat på träningsdata.
    + Fast prognostisering i det fall då datauppsättningen innehåller en kornkolonn, är denna korn av en numerisk typ och det finns ett mellanrum mellan tåg och testuppsättning
    + Fixade felmeddelandet om det duplicerade indexet i fjärrkörningen i prognosaktiviteter
    + Fast prognostisering i det fall då datauppsättningen innehåller en kornkolumn, är denna korn av en numerisk typ och det finns ett mellanrum mellan tåg och testuppsättning.
    + Fixade felmeddelandet om det duplicerade indexet i fjärrkörningen i prognosaktiviteter.
    + Lade till en skyddsräcke för att kontrollera om en datauppsättning är obalanserad eller inte. Om så är fallet, skulle ett skyddsräcke meddelande skrivas till konsolen.
  + **azureml-core**
    + Lade till möjligheten att hämta SAS-URL till modell i lagring via modellobjektet. Ex: model.get_sas_url()
    + Införa `run.get_details()['datasets']` för att hämta datauppsättningar som är associerade med den skickade körningen
    + Lägg `Dataset.Tabular.from_json_lines_files` till API för att skapa en Tabelldatauppsättning från JSON Lines-filer. Om du vill veta mer om dessa tabelldata i JSON https://aka.ms/azureml-data Lines filer på TabularDataset, besök för dokumentation.
    + Lade till ytterligare fält för vm-storlek (OS-disk, antal GPU:er) i funktionen supported_vmsizes ()
    + Lade till ytterligare fält i funktionen list_nodes () för att visa körningen, den privata och den offentliga IP-adressen, porten etc.
    + Möjlighet att ange ett nytt fält under klusteretablering --remotelogin_port_public_access som kan ställas in på aktiverad eller inaktiverad beroende på om du vill lämna SSH-porten öppen eller stängd när klustret skapas. Om du inte anger det öppnas eller stänger tjänsten porten på ett smart sätt beroende på om du distribuerar klustret i ett virtuella nätverk.
  + **azureml-explain-modell**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Lade till möjligheten att hämta SAS-URL till modell i lagring via modellobjektet. Ex: modell. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Införa kör. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['dataset'] för att hämta datauppsättningar som är associerade med den inlämnade körningen
    + Lägg `Dataset.Tabular`till API . [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) om du vill skapa en tabelldatauppsättning från JSON Lines-filer. Om du vill veta mer om dessa tabelldata i JSON https://aka.ms/azureml-data Lines filer på TabularDataset, besök för dokumentation.
    + Lade till ytterligare fält för vm-storlek (OS-disk, antal GPU:er) i funktionen [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Lade till ytterligare fält i funktionen [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) för att visa körningen, den privata och den offentliga IP-adressen, porten etc.
    + Möjlighet att ange ett nytt fält under [klusteretablering](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` som kan ställas in på aktiverad eller inaktiverad beroende på om du vill lämna SSH-porten öppen eller stängd när klustret skapas. Om du inte anger det öppnas eller stänger tjänsten porten på ett smart sätt beroende på om du distribuerar klustret i ett virtuella nätverk.
  + **azureml-explain-modell**
    + Förbättrad dokumentation för förklaringsutdata i klassificeringsscenariot.
    + Lade till möjligheten att ladda upp de förväntade y-värdena på förklaringen till utvärderingsexexemplen. Låser upp mer användbara visualiseringar.
    + Lade till förklaringsegenskapen i MimicWrapper för att kunna hämta underliggande MimicExplainer.
  + **azureml-pipeline-core**
    + Lade till anteckningsbok för att beskriva Module, ModuleVersion och ModuleStep
  + **azureml-pipeline-steg**
    + Lade till RScriptStep för att stödja R-skript som körs via AML-pipeline.
    + Fasta metadataparametrar tolkar i AzureBatchStep som orsakade felmeddelandet "tilldelning för parameter SubscriptionId har inte angetts."
  + **azureml-train-automl**
    + training_data, validation_data, label_column_name weight_column_name som datainmatningsformat som stöds
    + Lade till meddelande om utfasning för explain_model() och retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Lade till en [anteckningsbok](https://aka.ms/pl-modulestep) för att beskriva [Modul,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)) [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) och [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steg](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Lade till [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) för att stödja R-skript som körs via AML-pipeline.
    + Fasta metadataparametrar tolkar i [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) som orsakade felmeddelandet "tilldelning för parameter SubscriptionId har inte angetts".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + training_data validation_data label_column_name som stöd weight_column_name som datainmatningsformat som stöd.
    + Lade till meddelande om utfasning för [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) och [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK för Python v1.0.62

+ **Nya funktioner**
  + Introducerade egenskapen `timeseries` på TabularDataset. Den här egenskapen möjliggör enkel tidsstämpelfiltrering på data som en Tabelldatauppsättning, till exempel att ta alla data mellan ett tidsintervall eller de senaste data. Om du vill `timeseries` veta mer om detta egenskapen på TabularDataset, besök https://aka.ms/azureml-data för dokumentation eller https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb till exempel anteckningsbok.
  + Aktiverad utbildning med TabularDataset och FileDataset. Besök https://aka.ms/dataset-tutorial för ett exempel anteckningsbok.

  + **azureml-train-core**
      + Tillagd `Nccl` och `Gloo` stöd i PyTorch-estimator

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Inaktuella automl-inställningen "lag_length" och LaggingTransformer.
    + Fast korrekt validering av indata om de anges i ett dataflödesformat
    + Ändrade fit_pipeline.py för att generera grafjisonen och ladda upp till artefakter.
    + Återges diagrammet `userrun` under `Cytoscape`med hjälp av .
  + **azureml-core**
    + Revisited undantagshanteringen i ADB-kod och göra ändringar i enligt ny felhantering
    + Lade till automatisk MSI-autentisering för bärbara datorer.
    + Åtgärdar fel där skadade eller tomma modeller kan överföras på grund av misslyckade försök.
    + Fixade felet `DataReference` där namnet `DataReference` ändras när läget ändras (t.ex. när du ringer `as_upload`, `as_download`eller `as_mount`).
    + Gör `mount_point` `target_path` och `FileDataset.mount` valfritt för och `FileDataset.download`.
    + Undantag för att tidsstämpelkolumnen inte kan hittas kastas ut om tidsseriernas relaterade API anropas utan fin tidsstämpelkolumn tilldelad eller de tilldelade tidsstämpelkolumnerna tas bort.
    + Tidsseriekolumner ska tilldelas med kolumn vars typ är Datum, annars förväntas undantag
    + Tidsseriekolumner som tilldelar API 'with_timestamp_columns' kan ta Inget värde fint/grovt tidsstämpelkolumnnamn, som rensar tidigare tilldelade tidsstämpelkolumner.
    + Undantag kommer att kastas ut när antingen grovkornig eller finkornig tidsstämpel kolumn tas bort med indikation för användaren att släppa kan göras efter antingen exklusive tidsstämpel kolumn i släppa lista eller samtal with_time_stamp med Inget värde att släppa tidsstämpelkolumner
    + Undantaget kommer att kastas ut när antingen grovkornig eller finkornig tidsstämpelkolumn inte ingår i listan för behålla kolumner med uppgift om att behålla kan göras efter att antingen inkludera tidsstämpelkolumnen i listan behåll kolumn eller anropa with_time_stamp med Inget värde för att frigöra tidsstämpelkolumner.
    + Lade till loggning för storleken på en registrerad modell.
  + **azureml-explain-modell**
    + Fast varning tryckt till konsolen när "förpackning" python-paketet inte är installerat: "Använda äldre än stöds version av lightgbm, uppgradera till version större än 2.2.1"
    + Fast nedladdning modell förklaring med sharding för globala förklaringar med många funktioner
    + Fast härma explainer saknas initiering exempel på produktionen förklaring
    + Fast oföränderligt fel på inställda egenskaper vid uppladdning med förklaringsklient med hjälp av två olika typer av modeller
    + Lade till en get_raw param till scoring explainer .explain() så att en poäng explainer kan returnera både konstruerade och råa värden.
  + **azureml-train-automl**
    + Introducerade offentliga API:er från AutoML `automl` för att stödja förklaringar från förklara SDK - Nyare sätt att stödja AutoML-förklaringar genom att koppla automl-featurization och förklara SDK - Integrerat råförklaringsstöd från azureml förklarar SDK för AutoML-modeller.
    + Ta bort azureml-standardvärden från fjärrträningsmiljöer.
    + Ändrad standardcharchlagringsplats från FileCacheStore baserat en till AzureFileCacheStore en för AutoML på Azure Databricks-kodsökvägen.
    + Fast korrekt validering av indata om de anges i ett dataflödesformat
  + **azureml-train-core**
    + Återställd source_directory_data_store utfasning.
    + Lade till möjligheten att åsidosätta azureml-installerade paketversioner.
    + Lade till dockerfile-stöd i `environment_definition` parametern i skattningsansatorer.
    + Förenklade distribuerade utbildningsparametrar i skattningsmätare.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webbupplevelse (förhandsversion) för Azure Machine Learning-arbetsytor
Den nya webbupplevelsen gör det möjligt för datavetare och datatekniker att slutföra sin livscykel för maskininlärning från att förbereda och visualisera data till utbildning och distribution av modeller på en enda plats.

![Azure Machine Learning arbetsytegränssnitt (förhandsgranskning)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Viktiga funktioner:**

Med det här nya Azure Machine Learning-gränssnittet kan du nu:
+ Hantera dina anteckningsböcker eller länka ut till Jupyter
+ [Kör automatiserade ML-experiment](tutorial-first-experiment-automated-ml.md)
+ [Skapa datauppsättningar från lokala filer, datalager & webbfiler](how-to-create-register-datasets.md)
+ Utforska & förbereda datauppsättningar för att skapa modeller
+ Övervaka datadrift för dina modeller
+ Visa senaste resurser från en instrumentpanel

Vid tidpunkten för den här versionen stöds följande webbläsare: Chrome, Firefox, Safari och Microsoft Edge Preview.

**Kända problem:**

1. Uppdatera din webbläsare om du ser "Något gick fel! Det gick inte att läsa in segmentfiler" när distributionen pågår.

1. Det går inte att ta bort eller byta namn på filen i Anteckningsböcker och Filer. Under offentlig förhandsversion kan du använda Jupyter UI eller Terminal i notebook VM för att utföra uppdateringsfilåtgärder. Eftersom det är ett monterat nätverksfilsystem återspeglas alla ändringar som du gör på den bärbara datorns virtuella dator omedelbart i arbetsytan För bärbara datorer.

1. Så här SSH i den bärbara datorn:
   1. Hitta SSH-nycklarna som skapades under installationen av den virtuella datorn. Du kan också hitta nycklarna på arbetsytan Azure Machine Learning > öppna fliken Beräkna > hitta den virtuella datorn för anteckningsbok i listan > öppna dess egenskaper: kopiera nycklarna från dialogrutan.
   1. Importera dessa offentliga och privata SSH-nycklar till din lokala dator.
   1. Använd dem till SSH i den virtuella datorn för bärbara datorer.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK för Python v1.0.60

+ **Nya funktioner**
  + Introducerade FileDataset, som refererar till en eller flera filer i dina datalager eller offentliga webbadresser. Filerna kan vara av vilket format som helst. FileDataset ger dig möjlighet att ladda ner eller montera filerna till din beräkning. Om du vill veta mer https://aka.ms/file-datasetom FileDataset, besök .
  + Lagt till Pipeline Yaml-stöd för PythonScript-steg, Adla-steg, Databricks-steg, DataTransferStep och AzureBatch Steg

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + AutoArima är nu en anvisningsbar pipeline för förhandsversion.
    + Förbättrad felrapportering för prognoser.
    + Förbättrade loggningen med hjälp av anpassade undantag i stället för allmänna i prognosaktiviteterna.
    + Tog bort kontrollen på max_concurrent_iterations vara mindre än det totala antalet iterationer.
    + AutoML-modeller returnerar nu AutoMLExceptions
    + Den här versionen förbättrar körningsprestandan för automatiska datorinlärnings lokala körningar.
  + **azureml-core**
    + Introducera Dataset.get_all(arbetsyta), som returnerar en `TabularDataset` ordlista och `FileDataset` objekt som är knappade efter registreringsnamnet.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Införa `parition_format` som `Dataset.Tabular.from_delimited_files` argument `Dataset.Tabular.from_parquet.files`till och . Partitionsinformationen för varje datasökväg extraheras till kolumner baserat på det angivna formatet. {column_name}' skapar strängkolumn och {column_name:yyyy/MM/dd/HH/mm/ss}' skapar datetime-kolumn, där "yyyyy", "MM", "dd", "HH", "mm" och "ss" används för att extrahera år, månad, dag, timme, minut och sekund för datetime-typen. Den partition_format ska börja från positionen för den första partitionsnyckeln till slutet av filsökvägen. Med tanke på banan '.. /USA/2019/01/01/data.csv' där partitionen är efter land och tid, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' skapar strängkolumnen "Land" med värdet "USA" och datetime-kolumnen 'PartitionDate' med värdet '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Införa `partition_format` som `Dataset.Tabular.from_delimited_files` argument `Dataset.Tabular.from_parquet.files`till och . Partitionsinformationen för varje datasökväg extraheras till kolumner baserat på det angivna formatet. {column_name}' skapar strängkolumn och {column_name:yyyy/MM/dd/HH/mm/ss}' skapar datetime-kolumn, där "yyyyy", "MM", "dd", "HH", "mm" och "ss" används för att extrahera år, månad, dag, timme, minut och sekund för datetime-typen. Den partition_format ska börja från positionen för den första partitionsnyckeln till slutet av filsökvägen. Med tanke på banan '.. /USA/2019/01/01/data.csv' där partitionen är efter land och tid, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' skapar strängkolumnen "Land" med värdet "USA" och datetime-kolumnen 'PartitionDate' med värdet '2019-01-01'.
    + `to_csv_files`och `to_parquet_files` metoder har `TabularDataset`lagts till . Dessa metoder möjliggör `TabularDataset` konvertering `FileDataset` mellan a och a genom att konvertera data till filer i det angivna formatet.
    + Logga automatiskt in i basavbildningsregistret när du sparar en Dockerfile som genereras av Model.package().
    + "gpu_support" är inte längre nödvändigt. AML upptäcker nu automatiskt och använder nvidia docker-tillägget när det är tillgängligt. Det kommer att tas bort i en framtida version.
    + Lade till stöd för att skapa, uppdatera och använda PipelineDrafts.
    + Den här versionen förbättrar körningsprestandan för automatiska datorinlärnings lokala körningar.
    + Användare kan fråga mått från körhistorik efter namn.
    + Förbättrade loggningen med hjälp av anpassade undantag i stället för allmänna i prognosaktiviteterna.
  + **azureml-explain-modell**
    + Lade till feature_maps parametern till den nya MimicWrapper, så att användarna kan få raw-funktionsförklaringar.
    + Dataset-uppladdningar är nu inaktiverade som standard för förklaringsuppladdning och kan återaktiveras med upload_datasets=True
    + Lade till filtreringsparametrar för is_law i förklaringslistan och nedladdningsfunktionerna.
    + Lägger `get_raw_explanation(feature_maps)` till metod i både globala och lokala förklaringsobjekt.
    + La version kontrollera lightgbm med tryckt varning om nedan stöds version
    + Optimerad minnesanvändning vid batchförklaringar
    + AutoML-modeller returnerar nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Lagt till stöd för att skapa, uppdatera och använda PipelineDrafts - kan användas för att underhålla föränderliga pipelinedefinitioner och använda dem interaktivt för att köra
  + **azureml-train-automl**
    + Skapade funktion för att installera specifika versioner av GPU-kompatibla :::no-loc text="cuda"::: pytorch v1.1.0, verktygslåda 9.0, pytorch-transformers, som krävs för att aktivera BERT / XLNet i fjärr python runtime miljö.
  + **azureml-train-core**
    + Tidigt fel på vissa hyperparameter utrymme definition fel direkt i SDK i stället för serversidan.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Buggfixar och förbättringar**
  + Aktiverad skrivning till ADLS/ADLSGen2 med rå sökväg och autentiseringsuppgifter.
  + Fast en bugg `include_path=True` som orsakade `read_parquet`att inte fungera för .
  + Fast `to_pandas_dataframe()` fel som orsakades av undantaget "Ogiltigt egenskapsvärde: hostSecret".
  + Fixade ett fel där filer inte kunde läsas på DBFS i Spark-läge.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK för Python v1.0.57
+ **Nya funktioner**
  + Aktiverad `TabularDataset` för att förbrukas av AutomatedML. Om du `TabularDataset`vill veta https://aka.ms/azureml/howto/createdatasetsmer om, besök .

+ **Buggfixar och förbättringar**
  + **automl-klient-core-nativeclient**
    + Fixade felet, som togs upp när tränings- och/eller valideringsetiketter (y och y_valid) tillhandahålls i form av pandas dataram men inte som numpy array.
    + Uppdaterat gränssnitt för `RawDataContext` att skapa en `AutoMLBaseSettings` som bara kräver data och objektet.
    +  Tillåt AutoML-användare att släppa träningsserier som inte är tillräckligt långa när du förutser. - Tillåt AutoML-användare att släppa korn från testuppsättningen som inte finns i träningsuppsättningen när du förutser.
  + **azurblå-cli-ml**
    + Du kan nu uppdatera TLS/SSL-certifikatet för bedömningsslutpunkten som distribueras på AKS-klustret både för Microsoft-genererat certifikat och kundcertifikat.
  + **azureml-automl-core**
    + Åtgärdade ett problem i AutoML där rader med saknade etiketter inte togs bort korrekt.
    + Förbättrad felloggning i AutoML; fullständiga felmeddelanden kommer nu alltid att skrivas till loggfilen.
    + AutoML har uppdaterat sitt paket `azureml-defaults` `azureml-explain-model`fästa `azureml-dataprep`till att omfatta , och . AutoML varnar inte längre för paketfelmatchningar (förutom `azureml-train-automl` paket).
    + Fixade ett `timeseries` problem där cv-delningar är av olika storlek, vilket gör att lagerplatsberäkningen misslyckas.
    + När du kör ensemble iteration för Cross-Validation utbildning typ, om vi hamnade har problem med att ladda ner de modeller som utbildats på hela datauppsättningen, vi hade en inkonsekvens mellan modellen vikter och de modeller som matas in i omröstningen ensemble.
    + Fixade felet, som togs upp när tränings- och/eller valideringsetiketter (y och y_valid) tillhandahålls i form av pandas dataram men inte som numpy array.
    + Åtgärdade problemet med prognosaktiviteterna när Ingen påträffades i booleska kolumner i indatatabeller.
    + Tillåt AutoML-användare att släppa träningsserier som inte är tillräckligt långa när du förutser. - Tillåt AutoML-användare att släppa korn från testuppsättningen som inte finns i träningsuppsättningen när du förutser.
  + **azureml-core**
    + Åtgärdat problem med blob_cache_timeout parameterordning.
    + Lade till externa passnings- och omformnings undantagstyper till systemfel.
    + Lagt till stöd för Key Vault hemligheter för fjärrkörningar. Lägg till en azureml.core.keyvault.Keyvault-klass för att lägga till, hämta och lista hemligheter från keyvault som är associerade med arbetsytan. Åtgärder som stöds är:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(namn, värde)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(namn)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Ytterligare metoder för att få standard keyvault och få hemligheter under fjärrkörning:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(namn)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Lade till ytterligare åsidosättningsparametrar för att skicka-hyperdrive CLI-kommandot.
    + Förbättra tillförlitligheten i API-anrop utöka återförsök till vanliga begäranden bibliotek undantag.
    + Lägg till stöd för att skicka körs från en skickad körning.
    + Fast utgående SAS token problem i FileWatcher, vilket orsakade filer att sluta laddas upp efter deras första token hade upphört att gälla.
    + Importera HTTP csv/tsv-filer i datauppsättning python SDK stöds.
    + Inaktuell metoden Workspace.setup(). Varningsmeddelande som visas för användare föreslår att du använder create() eller get()/from_config() i stället.
    + Added Environment.add_private_pip_wheel(), som gör det möjligt `whl`att överföra privata anpassade python-paket till arbetsytan och säkert använda dem för att skapa/materialisera miljön.
    + Du kan nu uppdatera TLS/SSL-certifikatet för bedömningsslutpunkten som distribueras på AKS-klustret både för Microsoft-genererat certifikat och kundcertifikat.
  + **azureml-explain-modell**
    + Lade till parameter för att lägga till ett modell-ID i förklaringar vid uppladdning.
    + Lade `is_raw` till taggning i förklaringar i minnet och ladda upp.
    + Lade till pytorch-stöd och tester för azureml-explain-model-paket.
  + **azureml-opendatasets**
    + Stöd för identifiering och loggning av automatisk testmiljö.
    + Lade till klasser för att få amerikanska befolkningen efter län och zip.
  + **azureml-pipeline-core**
    + Lade till etikettegenskap i in- och utdataportdefinitioner.
  + **azureml-telemetry**
    + Fixade en felaktig telemetrikonfiguration.
  + **azureml-train-automl**
    + Fast felet där vid installationen fel, var felet inte få loggas i "fel" fält för installationen köra och därmed inte lagrades i överordnad körning "fel".
    + Åtgärdade ett problem i AutoML där rader med saknade etiketter inte togs bort korrekt.
    + Tillåt AutoML-användare att släppa träningsserier som inte är tillräckligt långa när du förutser.
    + Tillåt AutoML-användare att släppa korn från testuppsättningen som inte finns i träningsuppsättningen när du förutser.
    + Nu AutoMLStep `automl` passerar genom config till backend för att undvika problem på ändringar eller tillägg av nya config parametrar.
    + AutoML Data Guardrail är nu i offentlig förhandsversion. Användaren kommer att se en Data Guardrail-rapport (för klassificerings-/regressionsaktiviteter) efter träningen och även kunna komma åt den via SDK API.
  + **azureml-train-core**
    + La ficklampa 1,2 stöd i PyTorch Estimator.
  + **azureml-widgets**
    + Förbättrad förvirring matris diagram för klassificering utbildning.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Nya funktioner**
  + Listor över strängar kan nu skickas `read_*` in som indata till metoder.

+ **Buggfixar och förbättringar**
  + Prestandan `read_parquet` hos har förbättrats avsevärt när du kör i Spark.
  + Åtgärdade ett `column_type_builder` problem om det inte gick i händelse av en enskild kolumn med tvetydiga datumformat.

### <a name="azure-portal"></a>Azure Portal
+ **Funktionen Förhandsgranska**
  + Logg- och utdatafilsstreaming är nu tillgänglig för körningsinformationssidor. Filerna streamar uppdateringar i realtid när förhandsgranskningsväxlingen är aktiverad.
  + Möjligheten att ange kvot på arbetsytan släpps i förhandsversion. AmlCompute-kvoter tilldelas på prenumerationsnivå, men vi låter dig nu fördela kvoten mellan arbetsytor och allokera den för rättvis delning och styrning. Klicka bara på bladet **Usage+Kvoter** i arbetsytans vänstra navigeringsfält och välj fliken **Konfigurera kvoter.**

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK för Python v1.0.55

+ **Nya funktioner**
  + Tokenbaserad autentisering stöds nu för anrop som görs till bedömningsslutpunkten som distribueras på AKS. Vi kommer att fortsätta att stödja den aktuella nyckelbaserade autentiseringen och användare kan använda en av dessa autentiseringsmekanismer åt gången.
  + Möjlighet att registrera en blob-lagring som ligger bakom det virtuella nätverket (VNet) som ett datalager.

+ **Buggfixar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdar en bugg där valideringsstorleken för CV-delningar är liten och resulterar i dåligt förutsedda kontra sanna diagram för regression och prognoser.
    + Loggningen av prognosuppgifter på fjärrkontrollen körs förbättrats, nu användaren är försedd med omfattande felmeddelande om körningen misslyckades.
    + Fasta fel `Timeseries` i om förprocessflagga är Sant.
    + Gjorde vissa felmeddelanden för dataverifiering mer användbara.
    + Minskad minnesförbrukning av AutoML körs genom att släppa och / eller lat lastning av datamängder, särskilt mellan process spawns
  + **azureml-contrib-explain-model**
    + Lade till model_task flagga i explainers så att användaren kan åsidosätta standardlogik för automatisk inferens för modelltyp
    + Widget ändringar: Installeras automatiskt `contrib`med `nbextension` , inte mer installera / aktivera - stöd förklaring med bara global funktion betydelse (t.ex. permutativ)
    + Dashboard förändringar: - Box tomter och `beeswarm` tomter violin förutom tomt på sammanfattning `beeswarm` sida - Mycket snabbare återinlämnande av tomt på "Top -k" reglaget förändring - användbart meddelande som förklarar hur top-k beräknas - Användbara anpassningsbara meddelanden i stället för diagram när data inte tillhandahålls
  + **azureml-core**
    + Adderad Model.package()-metod för att skapa Docker-avbildningar och Dockerfiler som kapslar in modeller och deras beroenden.
    + Uppdaterade lokala webbtjänster för att acceptera InferenceConfigs som innehåller miljöobjekt.
    + Fast Model.register() som producerar ogiltiga modeller när '.' (för den aktuella katalogen) skickas som model_path parameter.
    + Lägg till Run.submit_child speglar funktionen Experiment.submit när du anger körningen som överordnad för den inskickade underordnade körningen.
    + Stöd konfigurationsalternativ från Model.register i Run.register_model.
    + Möjlighet att köra JAR-jobb i befintligt kluster.
    + Nu stöder instance_pool_id och cluster_log_dbfs_path parametrar.
    + Lade till stöd för att använda ett miljöobjekt när en modell distribueras till en webbtjänst. Miljöobjektet kan nu tillhandahållas som en del av InferenceConfig-objektet.
    + Lägg till appinsifht-kartläggning för nya regioner - centralus - westus - northcentralus
    + Lade till dokumentation för alla attribut i alla datalagerklasser.
    + Lade till blob_cache_timeout parameter `Datastore.register_azure_blob_container`i .
    + Lade till save_to_directory och load_from_directory metoder för azureml.core.environment.Environment.
    + Lade till kommandona "az ml environment download" och "az ml environment register" till CLI.
    + Lade till Environment.add_private_pip_wheel-metoden.
  + **azureml-explain-modell**
    + Lade till datauppsättningsspårning i Förklaringar med hjälp av datauppsättningstjänsten (förhandsversion).
    + Minskad standardbatchstorlek vid direktuppspelning av globala förklaringar från 10k till 100.
    + Lade till model_task flagga i explainers så att användaren kan åsidosätta standardinställningslogik för automatisk inferens för modelltyp.
  + **azureml-mlflow**
    + Fast fel i mlflow.azureml.build_image där kapslade kataloger ignoreras.
  + **azureml-pipeline-steg**
    + Lade till möjligheten att köra JAR-jobb i befintliga Azure Databricks-kluster.
    + Lade till stöd instance_pool_id och cluster_log_dbfs_path parametrar för DatabricksStep-steg.
    + Lade till stöd för pipelineparametrar i DatabricksStep-steg.
  + **azureml-train-automl**
    + Tillagd `docstrings` för ensemblerelaterade filer.
    + Uppdaterade dokument till lämpligare `max_cores_per_iteration` språk för och`max_concurrent_iterations`
    + Loggningen av prognosuppgifter på fjärrkontrollen körs förbättrats, nu användaren är försedd med omfattande felmeddelande om körningen misslyckades.
    + Tog bort get_data från `automlstep` bärbara rörledningen.
    + Startade `dataprep` support `automlstep`i .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Nya funktioner**
  + Du kan nu begära att utföra specifika inspektörer (t.ex. histogram, spridningsdiagram osv.) på specifika kolumner.
  + Lade till ett `append_columns`parallellisera argument i . Om sant läses data in i minnet, men körningen körs parallellt. Om False, kommer körningen att strömmas men entrådiga.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK för Python v1.0.53

+ **Nya funktioner**
  + Automatiserad maskininlärning stöder nu utbildning av ONNX-modeller på fjärrberäkningsmålet
  + Azure Machine Learning ger nu möjlighet att återuppta utbildning från en tidigare körning, kontrollpunkt eller modellfiler.
    + Lär dig hur du [använder kalkylatorer för att återuppta träningen från en tidigare körning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Buggfixar och förbättringar**
  + **automl-klient-core-nativeclient**
    + Åtgärda felet om att förlora kolumner typer efter omvandlingen (bugg länkade);
    + Tillåt y_query vara en objekttyp som innehåller ingen i början (#459519).
  + **azurblå-cli-ml**
    + CLI-kommandon "modell distribution" och "service update" accepterar nu parametrar, config filer, eller en kombination av de två. Parametrar har företräde framför attribut i filer.
    + Modellbeskrivning kan nu uppdateras efter registrering
  + **azureml-automl-core**
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (senaste).
    + Lägga till stöd för NimbusML-skattningsatorer & rörledningar som ska användas inom AutoML-skattningsmätare.
    + Fastställande av en bugg i Ensemble urvalsförfarandet som i onödan växte den resulterande ensemblen även om poängen förblev konstant.
    + Aktivera återanvändning av vissa featurizations över CV Splits för prognosaktiviteter. Detta snabbar upp körningen av installationen som körs av ungefär en faktor för n_cross_validations för dyra featurizations som fördröjningar och rullande fönster.
    + Åtgärda ett problem om tiden är på pandor som stöds tidsintervall. Vi höjer nu en DataException om tiden är mindre än pd. Tidsstämpel.min eller större än pd. Tidsstämpel.max
    + Prognoser tillåter nu olika frekvenser i tåg- och testuppsättningar om de kan justeras. Till exempel kan "kvartalsvis med början i januari" och vid "kvartalsvis med början i oktober" justeras.
    + Egenskapen "parametrar" har lagts till i TimeSeriesTransformer.
    + Ta bort gamla undantagsklasser.
    + I prognosaktiviteter accepterar parametern `target_lags` nu ett enda heltalsvärde eller en lista med heltal. Om heltalen angavs skapas bara en fördröjning. Om en lista anges tas de unika värdena för fördröjningar. target_lags=[1, 2, 2, 4] skapar fördröjningar på en, 2 och 4 perioder.
    + Åtgärda felet om att förlora kolumntyper efter omvandlingen (fellänkade);
    + I `model.forecast(X, y_query)`kan du tillåta y_query vara en objekttyp som innehåller Ingen (er) i början (#459519).
    + Lägga till `automl` förväntade värden i utdata
  + **azureml-contrib-datadrift**
    +  Förbättringar av exempel på anteckningsbok inklusive övergång till azureml-opendatasets istället för azureml-contrib-opendatasets och prestandaförbättringar vid berikande av data
  + **azureml-contrib-explain-model**
    + Argumentet Fasta omvandlingar för LIME-förklaring för råfunktionsbetydelse i azureml-contrib-explain-model-paket
    + Lade till segmentationer i avbildningsförklaringar i avbildningsut explainer för AzureML-contrib-explain-model-paketet
    + Lägg till scipy sparse stöd för LimeExplainer
    + Tillagd `batch_size` för `include_local=False`att efterlikna explainer när , för strömmande globala förklaringar i omgångar för att förbättra utförandetiden för DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Fix för att ringa set_featurizer_timeseries_params(): dict värdetyp förändring och `timeseries` null check - Lägg till anteckningsbok för featurizer
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (senaste).
  + **azureml-core**
    + Lade till möjligheten att koppla DBFS-datalager i AzureML CLI
    + Fixade felet med datalageruppladdning där `target_path` en tom mapp skapas om den startas med`/`
    + Åtgärdat `deepcopy` problem i ServicePrincipalAuthentication.
    + Lade till kommandona "az ml environment show" och "az ml environment list" till CLI.
    + Miljöer stöder nu att ange en base_dockerfile som ett alternativ till en redan byggd base_image.
    + Inställningen för oanvänd RunConfiguration auto_prepare_environment har markerats som inaktuell.
    + Modellbeskrivning kan nu uppdateras efter registrering
    + Bugfix: Modell och bildborttagning ger nu mer information om hur du hämtar uppströmsobjekt som är beroende av dem om borttagningen misslyckas på grund av ett uppströmsberoende.
    + Fast fel som skrivit ut tom varaktighet för distributioner som uppstår när du skapar en arbetsyta för vissa miljöer.
    + Förbättrade undantag för arbetsytan skapar fel. Sådana att användarna inte ser "Det går inte att skapa arbetsyta. Det gick inte att hitta..." meddelandet och i stället se det faktiska skapandefelet.
    + Lägg till stöd för tokenautentisering i AKS-webbtjänster.
    + Lägg `get_token()` till `Webservice` metod i objekt.
    + Lade till CLI-stöd för att hantera maskininlärningsdatauppsättningar.
    + `Datastore.register_azure_blob_container`nu tar ett `blob_cache_timeout` värde (i sekunder) som konfigurerar blobfuse s montera parametrar för att aktivera cache förfallodatum för detta datalager. Standard är ingen timeout, d.v.s. när en blob läss, stannar den i den lokala cachen tills jobbet är klart. De flesta jobb föredrar den här inställningen, men vissa jobb måste läsa mer data från en stor datauppsättning än vad som passar på deras noder. För dessa jobb hjälper justering av den här parametern dem att lyckas. Var försiktig när du ställer in det här parametern: om du ställer in värdet för lågt kan det leda till dåliga prestanda, eftersom de data som används i en epok kan upphöra innan de används igen. Det innebär att alla läsningar görs från blob-lagring (dvs. nätverket) i stället för den lokala cachen, vilket påverkar utbildningstiderna negativt.
    + Modellbeskrivning kan nu uppdateras korrekt efter registrering
    + Borttagning av modeller och bild ger nu mer information om uppströmsobjekt som är beroende av dem, vilket gör att borttagningen misslyckas
    + Förbättra resursutnyttjandet av fjärrkörningar med azureml.mlflow.
  + **azureml-explain-modell**
    + Argumentet Fasta omvandlingar för LIME-förklaring för råfunktionsbetydelse i azureml-contrib-explain-model-paket
    + lägga scipy gles stöd för LimeExplainer
    + la form linjär explainer omslag, samt en annan nivå till tabell explainer för att förklara linjära modeller
    + för härma explainer i förklara modellbibliotek, fast fel när include_local= Falskt för gles datainmatning
    + lägga till `automl` förväntade värden i utdata
    + fast permutationsfunktionsbetens betydelse när det finns argument för omvandlingar som levereras för att få råfunktionsbetydelse
    + läggas `batch_size` till för `include_local=False`att efterlikna explainer när , för streaming globala förklaringar i omgångar för att förbättra utförandetiden för DecisionTreeExplainableModel
    + för modell explainability bibliotek, fast blackbox explainers där pandor dataram indata krävs för förutsägelse
    + Fixade en `explanation.expected_values` bugg där ibland skulle returnera en flottör snarare än en lista med en flottör i den.
  + **azureml-mlflow**
    + Förbättra prestandan hos mlflow.set_experiment(experiment_name)
    + Åtgärda bugg vid användning av InteractiveLoginAuthentication för mlflow tracking_uri
    + Förbättra resursutnyttjandet av fjärrkörningar med azureml.mlflow.
    + Förbättra dokumentationen för azureml-mlflow-paketet
    + Patch bugg där mlflow.log_artifacts("my_dir") skulle spara artefakter under "my_dir / <artefakt-vägar>" i stället för "<artefakt-vägar>"
  + **azureml-opendatasets**
    + Fäst `pyarrow` `opendatasets` på gamla versioner (<0.14.0) på grund av minnesproblem som nyligen introducerades där.
    + Flytta azureml-contrib-opendatasets till azureml-opendatasets.
    + Tillåt att öppna datauppsättningsklasser registreras på Azure Machine Learning-arbetsytan och utnyttja AML-datauppsättningsfunktioner sömlöst.
    + Förbättra NoaaIsdWeather berika prestanda i icke-SPARK version avsevärt.
  + **azureml-pipeline-steg**
    + DBFS Datastore stöds nu för indata och utdata i DatabricksStep.
    + Uppdaterad dokumentation för Azure Batch Step när det gäller indata/utdata.
    + I AzureBatchStep *ändrades delete_batch_job_after_finish* standardvärdet till *true*.
  + **azureml-telemetry**
    +  Flytta azureml-contrib-opendatasets till azureml-opendatasets.
    + Tillåt att öppna datauppsättningsklasser registreras på Azure Machine Learning-arbetsytan och utnyttja AML-datauppsättningsfunktioner sömlöst.
    + Förbättra NoaaIsdWeather berika prestanda i icke-SPARK version avsevärt.
  + **azureml-train-automl**
    + Uppdaterad dokumentation om get_output för att återspegla den faktiska returtypen och ge ytterligare anteckningar om hur du hämtar viktiga egenskaper.
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (senaste).
    + lägga till `automl` förväntade värden i utdata
  + **azureml-train-core**
    + Strängar accepteras nu som beräkningsmål för automatiserad hyperparameterjustering
    + Inställningen för oanvänd RunConfiguration auto_prepare_environment har markerats som inaktuell.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Nya funktioner**
  + Lagt till stöd för att läsa en fil direkt från en http eller https-url.

+ **Buggfixar och förbättringar**
  + Förbättrat felmeddelande när du försöker läsa en parkettdatauppsättning från en fjärrkälla (som för närvarande inte stöds).
  + Fixade ett fel när du skrev till parettfilformat i ADLS Gen 2 och uppdaterade ADLS Gen 2-behållarnamnet i sökvägen.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuellt gränssnitt
+ **Förhandsgranska funktioner**
  + Lade till modulen Kör R-skript i visuellt gränssnitt.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK för Python v1.0.48

+ **Nya funktioner**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** är nu tillgänglig som **azureml-opendatasets**. Det gamla paketet kan fortfarande fungera, men vi rekommenderar att du använder **azureml-opendatasets** framåt för rikare funktioner och förbättringar.
    + Med det här nya paketet kan du registrera öppna datauppsättningar som datauppsättning i Azure Machine Learning-arbetsytan och utnyttja de funktioner som Dataset erbjuder.
    + Den innehåller också befintliga funktioner som att konsumera öppna datauppsättningar som Pandas/SPARK-dataramar och platskopplingar för vissa datauppsättningar som väder.

+ **Förhandsgranska funktioner**
    + HyperDriveConfig kan nu acceptera pipeline-objekt som en parameter för att stödja hyperparameterjustering med hjälp av en pipeline.

+ **Buggfixar och förbättringar**
  + **azureml-train-automl**
    + Fixade felet om att förlora kolumntyper efter omvandlingen.
    + Fixade felet så att y_query kunde vara en objekttyp som innehåller ingen i början.
    + Fixade frågan i ensemblevalsförfarandet som i onödan växte den resulterande ensemblen även om poängen förblev konstanta.
    + Åtgärdade problemet med whitelist_models och blacklist_models inställningar i AutoMLStep.
    + Åtgärdade problemet som förhindrade användningen av förbearbetning när AutoML skulle ha använts i kontexten för Azure ML Pipelines.
  + **azureml-opendatasets**
    + Flyttade azureml-contrib-opendatasets till azureml-opendatasets.
    + Tillåts att öppna datauppsättningsklasser registreras på Azure Machine Learning-arbetsytan och utnyttja AML-datauppsättningsfunktioner sömlöst.
    + Förbättrad NoaaIsdWeather berika prestanda i icke-SPARK version avsevärt.
  + **azureml-explain-modell**
    + Uppdaterad onlinedokumentation för tolkningsobjekt.
    + Läggs `batch_size` till för `include_local=False`att efterlikna explainer när , för strömmande globala förklaringar i omgångar för att förbättra körningstiden för DecisionTreeExplainableModel för modell explainability bibliotek.
    + Fixade problemet `explanation.expected_values` där ibland skulle returnera en flottör snarare än en lista med en flottör i den.
    + Lade till `automl` förväntade värden i utdata för att efterlikna explainer i förklaringsmodellbiblioteket.
    + Fast permutation funktion betydelse när omvandlingar argument levereras för att få rå funktionsvikt.
  + **azureml-core**
    + Lade till möjligheten att koppla DBFS-datalager i AzureML CLI.
    + Åtgärdade problemet med datalageruppladdning där `target_path` en `/`tom mapp skapas om den startas med .
    + Aktiverad jämförelse av två datauppsättningar.
    + Modell och bildborttagning ger nu mer information om hur du hämtar uppströmsobjekt som är beroende av dem om borttagningen misslyckas på grund av ett uppströmsberoende.
    + Inaktuell inställningen för oanvänd runkonfigurering i auto_prepare_environment.
  + **azureml-mlflow**
    + Förbättrad resursutnyttjande av fjärrkörningar som använder azureml.mlflow.
    + Förbättrade dokumentationen för azureml-mlflow-paketet.
    + Fixade problemet där mlflow.log_artifacts("my_dir") skulle spara artefakter under "my_dir/artifact-paths" i stället för "artefakt-paths".
  + **azureml-pipeline-core**
    + Parameter hash_paths för alla pipeline-steg är inaktuell och kommer att tas bort i framtiden. Som standard är innehållet i source_directory hashed (förutom filer som visas i .amlignore eller .gitignore)
    + Fortsatt förbättring Av Modul och ModuleStep för att stödja beräkningstypspecifika moduler, för att förbereda för RunConfiguration-integrering och andra ändringar för att låsa upp beräkningstypspecifik modulanvändning i pipelines.
  + **azureml-pipeline-steg**
    + AzureBatchStep: Förbättrad dokumentation när det gäller indata/utdata.
    + AzureBatchStep: Ändrat delete_batch_job_after_finish standardvärdet till true.
  + **azureml-train-core**
    + Strängar accepteras nu som beräkningsmål för automatiserad hyperparameterjustering.
    + Inaktuell inställningen för oanvänd runkonfigurering i auto_prepare_environment.
    + Inaktuella parametrar `conda_dependencies_file_path` och `pip_requirements_file_path` till `conda_dependencies_file` förmån `pip_requirements_file` för respektive.
  + **azureml-opendatasets**
    + Förbättra NoaaIsdWeather berika prestanda i icke-SPARK version avsevärt.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **Nya funktioner**
 + Dataflödesobjekt kan nu itereras över, vilket ger en sekvens av poster. Se dokumentation `Dataflow.to_record_iterator`för .
  + Dataflödesobjekt kan nu itereras över, vilket ger en sekvens av poster. Se dokumentation `Dataflow.to_record_iterator`för .

+ **Buggfixar och förbättringar**
 + Ökade robustheten hos DataPrep SDK.
 + Förbättrad hantering av pandor DataFrames med icke-strängkolumnindex.
 + Förbättrad prestanda `to_pandas_dataframe` för i datauppsättningar.
 + Fixade ett fel där Spark-körning av datauppsättningar misslyckades när den kördes i en flernodsmiljö.
  + Ökade robustheten hos DataPrep SDK.
  + Förbättrad hantering av pandor DataFrames med icke-strängkolumnindex.
  + Förbättrad prestanda `to_pandas_dataframe` för i datauppsättningar.
  + Fixade ett fel där Spark-körning av datauppsättningar misslyckades när den kördes i en flernodsmiljö.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

Vi återställde en förändring som förbättrade prestanda, eftersom det orsakade problem för vissa kunder som använder Azure Databricks. Om du har haft problem med Azure Databricks kan du uppgradera till version 1.1.7 med någon av metoderna nedan:
1. Kör det här skriptet för att uppgradera:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Återskapa klustret, som installerar den senaste Data Prep SDK-versionen.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK för Python v1.0.45

+ **Nya funktioner**
  + Lägg till ersättningsmodell för beslutsträd för att efterlikna explainer i azureml-explain-model-paket
  + Möjlighet att ange en CUDA-version som ska installeras på inferencing-bilder. Stöd för CUDA 9.0, 9.1 och 10.0.
  + Information om Azure ML-utbildningsbasavbildningar finns nu tillgängliga i [Azure ML Containers GitHub Repository](https://github.com/Azure/AzureML-Containers) och [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Lade till CLI-stöd för pipelineschema. Kör "az ml pipeline -h" för att lära dig mer
  + Lade till anpassad Kubernetes namnområdesparameter i AKS webbtjänstdistributionskonfiguration och CLI.
  + Inaktuell hash_paths parameter för alla pipeline-steg
  + Model.register stöder nu registrering av flera enskilda filer `child_paths` som en enda modell med hjälp av parametern.

+ **Förhandsgranska funktioner**
    + Scoring explainers kan nu eventuellt spara conda och pip information för mer tillförlitlig serialisering och deserialization.
    + Bug Fix för auto funktion selector.
    + Uppdaterad mlflow.azureml.build_image till det nya api:et, korrigerade fel som exponeras av den nya implementeringen.

+ **Buggfixar och förbättringar**
  + Tog `paramiko` bort beroende från azureml-core. Lade till varningar om utfasning för äldre beräkningsmålskopplingsmetoder.
  + Förbättra prestanda för run.create_children
  + I härma explainer med binär klassificerare, fixa ordningen på sannolikheter när lärare sannolikhet används för skalning formvärden.
  + Förbättrad felhantering och meddelande för automatiserad maskininlärning.
  + Fixade tidsgränsen för iteration för automatiserad maskininlärning.
  + Förbättrade omvandlingsprestanda för tidsserier för automatiserad maskininlärning.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **Nya funktioner**
  + Lade till sammanfattningsfunktioner`SummaryFunction.TOPVALUES`för högsta`SummaryFunction.BOTTOMVALUES`värden ( ) och bottenvärden ( ).

+ **Buggfixar och förbättringar**
  + Avsevärt förbättrat prestanda `read_pandas_dataframe`.
  + Fixade ett fel `get_profile()` som skulle orsaka på ett dataflöde som pekar på binära filer misslyckas.
  + Exponerad `set_diagnostics_collection()` för att möjliggöra programmatisk aktivering/inaktivering av telemetrisamlingen.
  + Ändrade beteendet `get_profile()`för . NaN-värden ignoreras nu för Min, Mean, Std och Sum, vilket stämmer överens med pandornas beteende.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK för Python v1.0.43

+ **Nya funktioner**
  + Azure Machine Learning ger nu förstklassigt stöd för populära ramverk för maskininlärning och dataanalys Scikit-learn. Med hjälp av [ `SKLearn` estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)kan användare enkelt träna och distribuera Scikit-learn-modeller.
    + Lär dig hur du [kör hyperparameterjustering med Scikit-learn med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Lade till stöd för att skapa ModuleStep i pipelines tillsammans med Module- och ModuleVersion-klasser för att hantera återanvändbara beräkningsenheter.
  + ACI-webbtjänster stöder nu beständiga scoring_uri via uppdateringar. Den scoring_uri ändras från IP till FQDN. Dns-namnetiketten för FQDN kan konfigureras genom att ställa in dns_name_label på deploy_configuration.
  + Automatiserad maskininlärning nya funktioner:
    + STL featurizer för prognoser
    + KMeans klustring är aktiverat för funktionssvepning
  + AmlCompute Kvot godkännanden blev bara snabbare! Vi har nu automatiserat processen för att godkänna dina kvotbegäranden inom ett tröskelvärde. Mer information om hur kvoter fungerar finns i [hur du hanterar kvoter](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Förhandsgranska funktioner**
    + Integrering med [MLflow](https://mlflow.org) 1.0.0-spårning via azureml-mlflow-paket ([exempel på anteckningsböcker](https://aka.ms/azureml-mlflow-examples)).
    + Skicka In Jupyter notebook som en körning. [Api-referensdokumentation](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Offentlig förhandsversion av [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) via azureml-contrib-datadrift-paket ( exempel[anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/monitor-models/data-drift)). Data Drift är en av de främsta anledningarna till att modellens noggrannhet försämras med tiden. Det händer när data som serveras till modell i produktion skiljer sig från de data som modellen har tränats på. AML Data Drift detektor hjälper kunden att övervaka data drift och skickar varning när drift upptäcks.

+ **Icke-bakåtkompatibla ändringar**

+ **Buggfixar och förbättringar**
  + RunConfiguration load and save stöder att ange en fullständig filsökväg med fullständig bakåtkompat för tidigare beteende.
  + Lade till cachelagring i ServicePrincipalAuthentication, inaktiverad som standard.
  + Aktivera loggning av flera ritplaner under samma måttnamn.
  + Modellklassen kan nu importeras korrekt`from azureml.core import Model`från azureml.core ( ).
  + I pipeline-steg är `hash_path` parametern nu föråldrad. Nytt beteende är att hash slutföra source_directory, utom filer som anges i .amlignore eller .gitignore.
  + I pipeline paket, `get_all` `get_all_*` olika och metoder har inaktuella `list` `list_*`till förmån för och , respektive.
  + azureml.core.get_run kräver inte längre att klasser importeras innan den ursprungliga körningstypen returneras.
  + Åtgärdade ett problem där vissa anrop till WebService Update inte utlöste en uppdatering.
  + Bedömning timeout på AKS webbtjänster bör vara mellan 5ms och 300000ms. Max får scoring_timeout_ms för poängförfrågningar har stötte från 1 min till 5 min.
  + LocalWebservice-objekt `scoring_uri` har `swagger_uri` nu och egenskaper.
  + Flyttad utdata katalog skapande och utdata katalog överföring ur användarprocessen. Aktiverad körningshistorik SDK att köra i varje användarprocess. Detta bör lösa vissa synkroniseringsproblem som upplevs av distribuerade utbildningskörningar.
  + Namnet på azureml-loggen som skrivits från användarnamnet kommer nu att innehålla processnamn (endast för distribuerad utbildning) och PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Buggfixar och förbättringar**
  + För tolkade datetime-värden som har ett tvåsiffrigt årsformat har intervallet för giltiga år uppdaterats för att matcha Windows May Release. Sortimentet har ändrats från 1930-2029 till 1950-2049.
  + När du läser i `handleQuotedLineBreaks=True` `\r` en fil och inställning behandlas den som en ny rad.
  + Fixade ett `read_pandas_dataframe` fel som orsakade att misslyckas i vissa fall.
  + Förbättrad prestanda `get_profile`för .
  + Förbättrade felmeddelanden.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **Nya funktioner**
  + Du kan nu använda följande uttrycksspråkfunktioner för att extrahera och tolka datumtidsvärden i nya kolumner.
    + `RegEx.extract_record()`extraherar datetime-element till en ny kolumn.
    + `create_datetime()`skapar datetime-objekt från separata datetime-element.
  + När `get_profile()`du ringer kan du nu se att kvantilkolumner är märkta som (est.) för att tydligt ange att värdena är approximationer.
  + Du kan nu använda ** globbing när du läser från Azure Blob Storage.
    + E.g.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Felkorrigeringar**
  + Fixade ett fel som var relaterat till att läsa en parkettfil från en fjärrkälla (Azure Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK för Python v1.0.39
+ **Ändringar**
  + Kör konfiguration auto_prepare_environment alternativet är inaktuell, med automatisk förbereda blir standard.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **Nya funktioner**
  + Lade till stöd för att läsa från en PostgresSQL-databas, antingen genom att ringa read_postgresql eller använda ett datalager.
    + Se exempel i hur du kan guidar:
      + [Anteckningsbok för datainmatning](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Bärbar datalager](https://aka.ms/aml-data-prep-datastore-nb)

+ **Buggfixar och förbättringar**
  + Fasta problem med konvertering av kolumntyp:
  + Nu konverteras en boolesk eller numerisk kolumn till en boolesk kolumn på rätt sätt.
  + Nu misslyckas inte när du försöker ange att en datumkolumn ska vara datumtyp.
  + Förbättrade JoinType-typer och medföljande referensdokumentation. När du ansluter till två dataflöden kan du nu ange en av följande typer av koppling:
    + INGEN, MATCH, INRE, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Förbättrad datatyp inferencing att känna igen fler datumformat.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

I Azure-portalen kan du nu:
+ Skapa och kör automatiserade ML-experiment
+ Skapa en virtuell dator för bärbara datorer för att prova exempel på Jupyter-anteckningsböcker eller dina egna.
+ Helt nytt redigeringsavsnitt (förhandsversion) på arbetsytan Azure Machine Learning, som inkluderar automatisk maskininlärning, visuellt gränssnitt och virtuella datorer med värddator
    + Skapa automatiskt en modell med automatiserad maskininlärning
    + Använda ett dra och släpp visuellt gränssnitt för att köra experiment
    + Skapa en virtuell dator för bärbar dator för att utforska data, skapa modeller och distribuera tjänster.
+ Live diagram och mått uppdatering i kör rapporter och köra informationssidor
+ Uppdaterade filvisningsprogram för loggar, utdata och ögonblicksbilder på sidor med körinformation.
+ Ny och förbättrad upplevelse för att skapa rapporter på fliken Experiment.
+ Lade till möjligheten att hämta filen config.json från sidan Översikt på arbetsytan Azure Machine Learning.
+ Stöd för att skapa Azure Machine Learning-arbetsytan från arbetsytan Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK för Python v1.0.33
+ **Nya funktioner**
  + _Metoden Workspace.create_ accepterar nu standardklusterkonfigurationer för CPU- och GPU-kluster.
  + Om skapandet av arbetsyta misslyckas rensas de resursberoende resurserna.
  + Standard-Azure Container Registry SKU har växlats till grundläggande.
  + Azure Container Registry skapas lättvindigt när det behövs för att köra eller skapa avbildning.
  + Stöd till miljöer för utbildningskörningar.

### <a name="notebook-virtual-machine"></a>Virtuell dator för bärbara datorer 

Använd en virtuell dator för bärbar dator som en säker, företagsklar värdmiljö för Jupyter-anteckningsböcker där du kan programmera maskininlärningsexperiment, distribuera modeller som webbslutpunkter och utföra alla andra åtgärder som stöds av Azure Machine Learning SDK med Python.Det ger flera funktioner:
+ [Snurra snabbt upp en förkonfigurerad virtuell dator](tutorial-1st-experiment-sdk-setup.md) för bärbara datorer som har den senaste versionen av Azure Machine Learning SDK och relaterade paket.
+ Åtkomst skyddas med beprövade tekniker, till exempel HTTPS, Azure Active Directory-autentisering och auktorisering.
+ Tillförlitlig molnlagring av anteckningsböcker och kod i ditt Azure Machine Learning Workspace-bloblagringskonto.Du kan ta bort den virtuella datorn utan att förlora ditt arbete på ett säkert sätt.
+ Förinstallerade exempeldatorböcker för att utforska och experimentera med Azure Machine Learning-funktioner.
+ Fullständiga anpassningsfunktioner för virtuella Azure-datorer, alla virtuella datorer, eventuella paket, eventuella drivrutiner. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK för Python v1.0.33 har släppts.

+ Azure ML Hardware Accelerated Models on [FPGA är](how-to-deploy-fpga-web-service.md) allmänt tillgänglig.
  + Du kan nu [använda azureml-accel-models-paketet](how-to-deploy-fpga-web-service.md) för att:
    + Träna vikterna för ett djupt neuralt nätverk som stöds (ResNet 50, ResNet 152, DenseNet-121, VGG-16 och SSD-VGG)
    + Använda överföringsinlärning med dnn som stöds
    + Registrera modellen med Model Management Service och behålla modellen
    + Distribuera modellen till en virtuell Azure-dator med en FPGA i ett AKS-kluster (Azure Kubernetes Service)
  + Distribuera behållaren till en [Azure Data Box Edge-serverenhet](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Poängsätta dina data med gRPC-slutpunkten med det här [exemplet](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

+ Funktionsvepning för :::no-loc text="featurizers"::: att möjliggöra dynamiskt lägger till för prestandaoptimering. Nytt :::no-loc text="featurizers":::: arbetsinbäddningar, bevisvikt, målkodningar, textmålkodning, klusteravstånd
+ Smart CV för att hantera tåg / giltiga delar inuti automatiserade ML
+ Få minnesoptimeringsändringar och prestandaförbättringar för körning
+ Prestandaförbättring i modellförklaring
+ ONNX modellkonvertering för lokal körning
+ Stöd för delförsampling
+ Intelligent stopp när inga exitkriterier har definierats
+ Staplade ensembler

+ Prognostisering för tidsserier
  + Ny prognosfunktion för förutsäga
  + Du kan nu använda korsvalidering med rullande ursprung på tidsseriedata
  + Nya funktioner som läggs till för att konfigurera fördröjningar i tidsserier
  + Nya funktioner som läggs till för att stödja aggregatfunktioner i rullande fönster
  + Ny helgidentifiering och featurizer när landskod definieras i experimentinställningar

+ Azure Databricks
  + Aktiverad tidsserieprognoser och modell explainabilty/interpretability kapacitet
  + Du kan nu avbryta och återuppta (fortsätta) automatiserade ML-experiment
  + Lagt till stöd för flerkärnbearbetning

### <a name="mlops"></a>MLOps (på ett sätt)
+ **Lokal distribution & felsökning för bedömning av behållare**<br/> Du kan nu distribuera en ML-modell lokalt och snabbt iterera på din bedömningsfil och beroenden för att säkerställa att de beter sig som förväntat.

+ **Introducerade InferenceConfig & Model.deploy()**<br/> Modelldistribution stöder nu att ange en källmapp med ett postskript, samma som en RunConfig.  Dessutom har modelldistributionen förenklats till ett enda kommando.

+ **Git referensspårning**<br/> Kunder har begärt grundläggande Git-integrationsfunktioner under en tid eftersom det hjälper till att upprätthålla en heltäckande granskningsspårning. Vi har implementerat spårning över större entiteter i Azure ML för Git-relaterade metadata (repo, commit, clean state). Denna information samlas in automatiskt av SDK och CLI.

+ **Modellprofilering & valideringstjänst**<br/> Kunder klagar ofta på svårigheten att korrekt storlek beräkna i samband med deras slutledning tjänst. Med vår modell profilering tjänst, kan kunden ge provindata och vi kommer att profilera över 16 olika CPU / minneskonfigurationer för att bestämma optimal storlek för distribution.

+ **Ta med egen basbild för slutledning**<br/> Ett annat vanligt klagomål var svårigheten att gå från experiment till inferens RE dela beroenden. Med vår nya basbilddelningskapacitet kan du nu återanvända dina experimentbasbilder, beroenden och allt, för slutledning. Detta bör påskynda distributioner och minska gapet från den inre till den yttre slingan.

+ **Förbättrad Swagger schema generation erfarenhet**<br/> Vår tidigare swagger generation metod var felbenägen och omöjligt att automatisera. Vi har ett nytt in-line sätt att generera swagger scheman från någon Python funktion via dekoratörer. Vi har den här koden med öppen källkod och vårt schemagenereringsprotokoll är inte kopplat till Azure ML-plattformen.

+ **Azure ML CLI är allmänt tillgängligt (GA)**<br/> Modeller kan nu distribueras med ett enda CLI-kommando. Vi fick gemensam kundfeedback att ingen distribuerar en ML-modell från en Jupyter bärbar dator. [**CLI-referensdokumentationen**](https://aka.ms/azmlcli) har uppdaterats.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK för Python v1.0.30 har släppts.

Den [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) introducerades för att lägga till en ny version av en publicerad pipeline samtidigt som samma slutpunkt bibehålls.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Data Prep Python SDK installeras `numpy` inte `pandas` längre och paket. Se [uppdaterade installationsinstruktioner](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nya funktioner**
  + Nu kan du använda pivottransformen.
    + Guide: Pivot [anteckningsbok](https://aka.ms/aml-data-prep-pivot-nb)
  + Du kan nu använda reguljära uttryck i inbyggda funktioner.
    + Exempel:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Du kan `to_upper`  nu `to_lower`  använda och funktioner i uttrycksspråk.
  + Du kan nu se antalet unika värden för varje kolumn i en dataprofil.
  + För några av de vanliga läsarstegen kan `infer_column_types` du nu skicka in argumentet. Om den är `True`inställd på kommer Data Prep att försöka identifiera och automatiskt konvertera kolumntyper.
    + `inference_arguments`är nu föråldrad.
  + Du kan `Dataflow.shape`nu ringa .

+ **Buggfixar och förbättringar**
  + `keep_columns` accepterar nu ytterligare `validate_column_exists`ett valfritt argument `keep_columns` , som kontrollerar om resultatet av kommer att innehålla några kolumner.
  + Alla läsarsteg (som läs från en fil) accepterar nu ytterligare ett valfritt argument `verify_exists`.
  + Förbättrad prestanda för att läsa från pandas dataram och få dataprofiler.
  + Fixade ett fel där det gick ett steg från ett dataflöde misslyckades med ett enda index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Du kan nu skicka in en befintlig skriptkörning på ett befintligt fjärrberäkningskluster.
  + Du kan nu köra en publicerad pipeline med nya parametrar på fliken Pipelines.
  + Kör information stöder nu en ny Snapshot-filvisare. Du kan visa en ögonblicksbild av katalogen när du skickade en viss körning. Du kan också hämta anteckningsboken som skickades för att starta körningen.
  + Du kan nu avbryta överordnade körningar från Azure-portalen.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK för Python v1.0.23

+ **Nya funktioner**
  + Azure Machine Learning SDK stöder nu Python 3.7.
  + Azure Machine Learning DNN-estimatorer ger nu inbyggt stöd för flera versioner. Till exempel `TensorFlow`  accepterar estimator `framework_version` nu en parameter och användarna kan ange version "1.10" eller "1.12". Om du vill ha en lista över de `get_supported_versions()` versioner som stöds av den `TensorFlow.get_supported_versions()`aktuella SDK-versionen anropar du önskad ramklass (till exempel ).
  En lista över de versioner som stöds av den senaste SDK-versionen finns i [dokumentationen till DNN-uppskattningor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **Nya funktioner**
  + Du kan läsa flera datalager-/datapath/datareferenskällor med hjälp av read_* transformeringar.
  + Du kan utföra följande åtgärder i kolumner för att skapa en ny kolumn: division, golv, modulo, effekt, längd.
  + Data Prep är nu en del av Azure ML diagnostikpaketet och loggar diagnostikinformation som standard.
    + Om du vill inaktivera den här miljön ställer du in den här miljövariabeln på true: DISABLE_DPREP_LOGGER

+ **Buggfixar och förbättringar**
  + Förbättrad koddokumentation för vanliga klasser och funktioner.
  + Fixade ett fel i auto_read_file som inte kunde läsa Excel-filer.
  + Lade till alternativet om du vill skriva över mappen i read_pandas_dataframe.
  + Förbättrad prestanda för dotnetcore2 beroende installation, och lagt till stöd för Fedora 27/28 och Ubuntu 1804.
  + Förbättrad prestanda för läsning från Azure Blobbar.
  + Identifiering av kolumntyp stöder nu kolumner av typen Lång.
  + Fixade ett fel där vissa datumvärden visades som tidsstämplar i stället för Python-datetime-objekt.
  + Fixade ett fel där vissa typantal visades som dubbel i stället för heltal.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK för Python v1.0.21

+ **Nya funktioner**
  + *Azureml.core.Run.create_children-metoden* gör det möjligt att skapa flera underordnade körningar med låg latens med ett enda anrop.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Icke-bakåtkompatibla ändringar**
  + Konceptet med Data Prep-paketet har inaktuellt och stöds inte längre. I stället för att spara flera dataflöden i ett paket kan du spara dataflöden individuellt.
    + Guide: Öppna [och spara dataflöden anteckningsbok](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nya funktioner**
  + Data Prep kan nu känna igen kolumner som matchar en viss semantisk typ och dela därefter. De STypes som för närvarande stöds inkluderar: e-postadress, geografiska koordinater (latitud & longitud), IPv4- och IPv6-adresser, amerikanskt telefonnummer och amerikanskt postnummer.
    + Så här guidar du: [Anteckningsbok för semantiska typer](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep stöder nu följande åtgärder för att generera en resulterande kolumn från två numeriska kolumner: subtrahera, multiplicera, dela och modulo.
  + Du kan `verify_has_data()` anropa ett dataflöde för att kontrollera om dataflödet skulle producera poster om det körs.

+ **Buggfixar och förbättringar**
  + Du kan nu ange hur många lagerplatser som ska användas i ett histogram för numeriska kolumnprofiler.
  + Transformeringen `read_pandas_dataframe` kräver nu att DataFrame har sträng- eller byte-typade kolumnnamn.
  + Fixade ett `fill_nulls` fel i transformeringen, där värdena inte fylldes korrekt i om kolumnen saknades.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK för Python v1.0.18

 + **Ändringar**
   + Azureml-tensorboard-paketet ersätter azureml-contrib-tensorboard.
   + Med den här versionen kan du konfigurera ett användarkonto på det hanterade beräkningsklustret (amlcompute) samtidigt som du skapar det. Detta kan göras genom att skicka dessa egenskaper i etableringskonfigurationen. Mer information finns i [SDK-referensdokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nya funktioner**
  + Nu stöder att lägga till två numeriska kolumner för att generera en resulterande kolumn med hjälp av uttrycksspråket.

+ **Buggfixar och förbättringar**
  + Förbättrade dokumentationen och parameterkontrollen för random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Bugg fix**
  + Fixade ett autentiseringsproblem för tjänsthuvudnamn som orsakades av en API-ändring.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK för Python v1.0.17

+ **Nya funktioner**

  + Azure Machine Learning ger nu förstklassigt stöd för populära DNN framework Chainer. Med [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) hjälp av klassanvändare kan du enkelt träna och distribuera Chainer-modeller.
    + Lär dig hur du [kör distribuerad träning med ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Lär dig hur du [kör hyperparameterjustering med Chainer med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines har lagt till möjligheten att utlösa en Pipeline-körning baserat på ändringar i datalager. Anteckningsboken för [pipelineschema](https://aka.ms/pl-schedule) uppdateras för att visa upp den här funktionen.

+ **Buggfixar och förbättringar**
  + Vi har lagt till stöd i Azure Machine Learning-pipelines för att ange source_directory_data_store egenskapen till ett önskat datalager (till exempel en blob-lagring) på [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) som levereras till [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Som standard använder steg Azure File Store som bakgrundsdatalager, som kan stöta på begränsningsproblem när ett stort antal steg körs samtidigt.

### <a name="azure-portal"></a>Azure Portal

+ **Nya funktioner**
  + Ny dra- och släpptabellredigerare för rapporter. Användare kan dra en kolumn från brunnen till tabellområdet där en förhandsgranskning av tabellen visas. Kolumnerna kan ordnas om.
  + Ny loggfilsvisning
  + Länkar till experimentkörningar, beräkning, modeller, avbildningar och distributioner från fliken Aktiviteter

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Nya funktioner**
  + Data Prep stöder nu att skriva filströmmar från ett dataflöde. Ger också möjlighet att manipulera filströmsnamnen för att skapa nya filnamn.
    + Guide: Arbeta [med anteckningsbok för filströmmar](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Buggfixar och förbättringar**
  + Förbättrad prestanda för t-Digest på stora datamängder.
  + Data Prep stöder nu läsning av data från en DataPath.
  + En het kodning fungerar nu på booleska och numeriska kolumner.
  + Andra diverse buggfixar.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK för Python v1.0.15

+ **Nya funktioner**
  + Azure Machine Learning Pipelines har lagt till AzureBatchStep ([anteckningsbok),](https://aka.ms/pl-azbatch)HyperDriveStep (anteckningsbok) och tidsbaserade schemaläggningsfunktioner ([anteckningsbok](https://aka.ms/pl-schedule)).
  +  DataTranferStep uppdateras för att fungera med Azure SQL Server och Azure-databas för PostgreSQL ([anteckningsbok](https://aka.ms/pl-data-trans)).

+ **Ändringar**
  + Föråldrad till `PublishedPipeline.get_published_pipeline` förmån `PublishedPipeline.get`för .
  + Föråldrad till `Schedule.get_schedule` förmån `Schedule.get`för .

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nya funktioner**
  + Data Prep stöder nu läsning från en Azure SQL-databas med Datastore.

+ **Ändringar**
  + Förbättrade minnesprestanda för vissa åtgärder på stora data.
  + `read_pandas_dataframe()`nu `temp_folder` måste specificeras.
  + Egenskapen `name` `ColumnProfile` på har inaktuell `column_name` - använd istället.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK för Python v1.0.10

+ **Ändringar**:
  + Azure ML SDK har inte längre azure-cli-paket som beroende. Azure-cli-core- och azure-cli-profile-beroenden har tagits bort från azureml-core. Det här är användaren som påverkar ändringarna:
      + Om du utför "az login" och sedan använder azureml-sdk, kommer SDK att göra webbläsaren eller enhetskoden logga in en gång till. Det kommer inte att använda några autentiseringsuppgifter tillstånd som skapats av "az login".
    + För Azure CLI-autentisering, till exempel använda "az login", använder _azureml.core.authentication.AzureCliAuthentication_ class. För Azure CLI-autentisering installerar _du azure-cli i Python-miljön_ där du har installerat azureml-sdk.
    + Om du gör "az login" med hjälp av ett tjänsthuvudnamn för automatisering rekommenderar vi att du använder _azureml.core.authentication.ServicePrincipalAuthentication-klass,_ eftersom azureml-sdk inte använder autentiseringstillstånd som skapats av azure CLI.

+ **Buggfixar:** Den här versionen innehåller mestadels mindre buggfixar

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Felkorrigeringar**
  + Förbättrad prestanda för att få dataprofiler.
  + Åtgärdade mindre fel relaterade till felrapportering.

### <a name="azure-portal-new-features"></a>Azure-portal: nya funktioner
+ Ny dra- och släppdiagramupplevelse för rapporter. Användare kan dra en kolumn eller ett attribut från brunnen till diagramområdet där systemet automatiskt väljer en lämplig diagramtyp för användaren baserat på typen av data. Användare kan ändra diagramtypen till andra tillämpliga typer eller lägga till ytterligare attribut.

    Diagramtyper som stöds:
    - Linjediagram
    - Histogram
    - Staplat stapeldiagram
    - Box Tomt
    - Punkt tomt
    - Bubbla tomt
+ Portalen genererar nu dynamiskt rapporter för experiment. När en användare skickar en körning till ett experiment genereras en rapport automatiskt med loggade mått och diagram för att tillåta jämförelse mellan olika körningar.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK för Python v1.0.8

+ **Buggfixar:** Den här versionen innehåller mestadels mindre buggfixar

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Nya funktioner**
  + Förbättringar av datalager (dokumenteras i [Datastore how-to-guide](https://aka.ms/aml-data-prep-datastore-nb))
    + Lade till möjlighet att läsa från och skriva till Azure File Share och ADLS-datalager i skala upp.
    + När datalager används stöder Data Prep nu att använda tjänstens huvudautentisering i stället för interaktiv autentisering.
    + Lagt till stöd för wasb och wasbs urls.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Felkorrigeringar**
  + Fast bugg med läsning från offentliga läsbara Azure Blob-behållare på Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK för Python v1.0.6
+ **Buggfixar:** Den här versionen innehåller mestadels mindre buggfixar

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Nya funktioner**
  + `to_bool`funktionen gör det nu möjligt att konvertera inkompatibla värden till felvärden. Detta är det nya standardfelmatchningsbeteendet för `to_bool` och `set_column_types`, medan det tidigare standardbeteendet var att konvertera felaktiga värden till False.
  + När `to_pandas_dataframe`du anropar finns det ett nytt alternativ för att tolka null/missing-värden i numeriska kolumner som NaN.
  + Lade till möjligheten att kontrollera returtypen för vissa uttryck för att säkerställa typkonsekvens och misslyckas tidigt.
  + Du kan `parse_json` nu anropa för att tolka värden i en kolumn som JSON-objekt och expandera dem till flera kolumner.

+ **Felkorrigeringar**
  + Fixade en bugg `set_column_types` som kraschade i Python 3.5.2.
  + Fixade ett fel som kraschade när du anslöt till Datastore med en AML-avbildning.

+ **Uppdateringar**
  * [Exempel på anteckningsböcker](https://aka.ms/aml-data-prep-notebooks) för att komma igång med självstudier, fallstudier och instruktioner.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Allmän tillgänglighet

Azure Machine Learning är nu allmänt tillgängligt.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
Med den här versionen presenterar vi en ny hanterad beräkningsupplevelse via [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). Det här beräkningsmålet ersätter Azure Batch AI-beräkning för Azure Machine Learning.

Det här beräkningsmålet:
+ Används för modellträning och batch inferens/poängsättning
+ Är beräkning av en-till-flera nod
+ Gör klusterhantering och finplanering för användaren
+ Automatiska skalningar som standard
+ Stöd för både CPU- och GPU-resurser
+ Möjliggör användning av virtuella datorer med låg prioritet för lägre kostnad

Azure Machine Learning Compute kan skapas i Python med Hjälp av Azure-portalen eller CLI. Den måste skapas i arbetsytan och kan inte kopplas till någon annan arbetsyta. Det här beräkningsmålet använder en Docker-behållare för körningen och paketerar dina beroenden för att replikera samma miljö i alla dina noder.

> [!Warning]
> Vi rekommenderar att du skapar en ny arbetsyta för att använda Azure Machine Learning Compute. Det finns en stor risk att användare som försöker skapa Azure Machine Learning Compute från en befintlig arbetsyta kan se ett fel. Befintlig beräkning på arbetsytan bör fortsätta att fungera opåverkad.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK för Python v1.0.2
+ **Icke-bakåtkompatibla ändringar**
  + Med den här versionen tar vi bort stöd för att skapa en virtuell dator från Azure Machine Learning. Du kan fortfarande ansluta en befintlig virtuell dator i molnet eller en fjärrbaserad server.
  + Vi tar också bort stöd för BatchAI, som alla bör stödjas via Azure Machine Learning Compute nu.

+ **Ny**
  + För maskininlärningspipelor:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep (300)](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Uppdaterad**
  + För maskininlärningspipelor:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) accepterar nu runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) kopierar nu till och från en SQL-datakälla
    + Schemalägga funktioner i SDK för att skapa och uppdatera scheman för att köra publicerade pipelines

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Icke-bakåtkompatibla ändringar**
  * `SummaryFunction.N`har bytt `SummaryFunction.Count`namn till .

+ **Buggfixar**
  * Använd den senaste AML Run Token när du läser från och skriver till datalager på fjärrkörningar. Tidigare, om AML Run Token uppdateras i Python, kommer Data Prep-körningen inte att uppdateras med den uppdaterade AML Run Token.
  * Ytterligare tydligare felmeddelanden
  * to_spark_dataframe() kraschar inte längre när Spark `Kryo` använder serialisering
  * Value Count Inspector kan nu visa mer än 1000 unika värden
  * Random Split misslyckas inte längre om det ursprungliga dataflödet inte har ett namn

+ **Läs mer**
  * [Azure Machine Learning-SDK för dataförberedelse](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokument och anteckningsböcker
+ ML Rörledningar
  + Nya och uppdaterade anteckningsböcker för att komma igång med pipelines, batch-omfång och formatöverföringsexempel:https://aka.ms/aml-pipeline-notebooks
  + Lär dig hur du [skapar din första pipeline](how-to-create-your-first-pipeline.md)
  + Lär dig hur du [kör batchförutsägelser med pipelines](how-to-use-parallel-run-step.md)
+ Beräkningsmål för Azure Machine Learning
  + [Exempel anteckningsböcker uppdateras](https://aka.ms/aml-notebooks) nu för att använda den nya hanterade beräkningen.
  + [Läs mer om den här beräkningen](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-portal: nya funktioner
+ Skapa och hantera [Azure Machine Learning Compute-typer](how-to-set-up-training-targets.md#amlcompute) i portalen.
+ Övervaka kvotanvändning och [begär kvot](how-to-manage-quotas.md) för Azure Machine Learning Compute.
+ Visa Azure Machine Learning Compute-klusterstatus i realtid.
+ Stöd för virtuellt nätverk har lagts till för Azure Machine Learning Compute och Azure Kubernetes Service.Virtual network support was added for Azure Machine Learning Compute and Azure Kubernetes Service creation.
+ Kör dina publicerade pipelines med befintliga parametrar.
+ Nya [automatiserade maskininlärningsdiagram](how-to-understand-automated-ml.md) för klassificeringsmodeller (lyft, vinster, kalibrering, funktionsviktsdiagram med modellförklaringbarhet) och regressionsmodeller (rester och funktionsviktdiagram med modell explainability).
+ Pipelines kan visas i Azure Portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK för Python v0.1.80

+ **Icke-bakåtkompatibla ändringar**
  * *azureml.train.widgets* namnområde har flyttats till *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* inaktuella följande klasser - *azureml.core.compute.BatchAICompute* och *azureml.core.compute.DSVMCompute*. Den senare klassen kommer att tas bort i efterföljande versioner. Klassen AmlCompute har en enklare definition nu och behöver helt enkelt en vm_size och max_nodes, och skalar automatiskt klustret från 0 till max_nodes när ett jobb skickas in. Våra [exempel anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) har uppdaterats med denna information och bör ge dig användning exempel. Vi hoppas att du gillar denna förenkling och massor av mer spännande funktioner att komma i en senare release!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1

Läs mer om Data Prep SDK genom att läsa [referensdokument](https://aka.ms/data-prep-sdk).
+ **Nya funktioner**
   * Skapade en ny DataPrep CLI för att köra DataPrep-paket och visa dataprofilen för en datauppsättning eller ett dataflöde
   * Omdesignade SetColumnType API för att förbättra användbarheten
   * Bytt namn smart_read_file till auto_read_file
   * Nu ingår skeva och kurtosis i dataprofilen
   * Kan prov med stratifierad provtagning
   * Kan läsa från zip-filer som innehåller CSV-filer
   * Kan dela datauppsättningar radvis med Random Split (till exempel i test-tåg uppsättningar)
   * Kan hämta alla kolumndatatyper från ett dataflöde eller en dataprofil genom att ringa`.dtypes`
   * Kan hämta radantalet från ett dataflöde eller en dataprofil genom att ringa`.row_count`

+ **Buggfixar**
   * Fast lång till dubbel konvertering
   * Fast gällande efter en tilläggskolumn
   * Åtgärdade ett problem med FuzzyGrouping, där det inte skulle upptäcka grupper i vissa fall
   * Fast sorteringsfunktion för att respektera sorteringsordning för flera kolumner
   * Fasta och/eller uttryck som liknar `pandas` hur hanterar dem
   * Fast läsning från dbfs-sökvägen
   * Gjorde felmeddelanden mer begripliga
   * Nu misslyckas inte längre när du läser på fjärrberäkningsmål med hjälp av en AML-token
   * Nu inte längre misslyckas på Linux DSVM
   * Nu kraschar inte längre när icke-strängvärden är i sträng predikat
   * Hanterar nu kontrollfel när Dataflow ska misslyckas korrekt
   * Nu stöder dbutils monterade lagringsplatser på Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
Azure-portalen för Azure Machine Learning har följande uppdateringar:
  * En ny **pipelines-flik** för publicerade pipelines.
  * Lade till stöd för att koppla ett befintligt HDInsight-kluster som ett beräkningsmål.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK för Python v0.1.74

+ **Icke-bakåtkompatibla ändringar**
  * *Workspace.compute_targets, datalager, experiment, bilder, modeller och *webbtjänster* är egenskaper i stället för metoder. Ersätt till exempel *Workspace.compute_targets()* med *Workspace.compute_targets*.
  * *Run.get_context* inaktuella *Run.get_submitted_run*. Den senare metoden kommer att tas bort i efterföljande versioner.
  * *Klassen PipelineData* förväntar sig nu ett datalagerobjekt som en parameter i stället för datastore_name. På samma sätt accepterar *Pipeline* default_datastore i stället för default_datastore_name.

+ **Nya funktioner**
  * [Exempeldatorn](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) för Azure Machine Learning Pipelines använder nu MPI-steg.
  * Widgeten RunDetails för Jupyter-anteckningsböcker uppdateras för att visa en visualisering av pipelinen.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0

+ **Nya funktioner**
  * Skrivantal som lagts till i dataprofilen
  * Värderäkning och histogram är nu tillgängligt
  * Fler percentiler i dataprofilen
  * Medianen är tillgänglig i Summarize
  * Python 3.7 stöds nu
  * När du sparar ett dataflöde som innehåller datalager i ett DataPrep-paket sparas datalagrets information som en del av DataPrep-paketet
  * Det stöds nu att skriva till datalager

+ **Bugg fast**
  * 64-bitars osignerade heltalsspill hanteras nu korrekt på Linux
  * Fast felaktig textetikett för oformaterade textfiler i smart_read
  * Strängkolumntypen visas nu i måttvyn
  * Typantalet nu är fast för att visa ValueKinds mappade till enstaka FieldType i stället för enskilda
  * Write_to_csv inte längre misslyckas när sökvägen anges som en sträng
  * När du använder Ersätt misslyckas inte längre "hitta" tomt

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK för Python v0.1.68

+ **Nya funktioner**
  * Stöd för flera innehavare när du skapar en ny arbetsyta.

+ **Buggar fast**
  * Du behöver inte längre fästa pynacl-biblioteksversionen när du distribuerar webbtjänsten.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **Nya funktioner**
  * Lade till metod transform_partition_with_file(script_path), vilket gör att användare kan passera i sökvägen till en Python-fil för att köra

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK för Python v0.1.65
[Version 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) innehåller nya funktioner, mer dokumentation, buggfixar och fler [exempel anteckningsböcker](https://aka.ms/aml-notebooks).

Se [listan över kända problem](resource-known-issues.md) för att lära dig mer om kända buggar och lösningar.

+ **Icke-bakåtkompatibla ändringar**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services return dictionary, tidigare returned list. Se [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-dokumentation.

  * Automatiserad maskininlärning tog bort normaliserat medelvadsfel från de primära måtten.

+ **Hyperdrive**
  * Olika HyperDrive buggfixar för Bayesian, Prestandaförbättringar för hämta Metrics-anrop.
  * Tensorflow 1,10 uppgradering från 1,9
  * Docker bildoptimering för kallstart.
  * Jobb rapporterar nu rätt status även om de avslutas med annan felkod än 0.
  * RunConfig-attributvalidering i SDK.
  * HyperDrive-körningsobjektet stöder avbrytas som liknar en vanlig körning: du behöver inte skicka några parametrar.
  * Widgetförbättringar för att upprätthålla läget för listvärden för distribuerade körningar och HyperDrive-körningar.
  * TensorBoard och andra loggfiler stöder fast för Parameter server.
  * Intel(R) MPI-stöd på tjänstsidan.
  * Bugfix till parameterjustering för distribuerad körningskorrigering under validering i BatchAI.
  * Context Manager identifierar nu den primära instansen.

+ **Azure portal-upplevelse**
  * log_table() och log_row() stöds i Kör information.
  * Skapa automatiskt diagram för tabeller och rader med 1, 2 eller 3 numeriska kolumner och en valfri kategorisk kolumn.

+ **Automatiserad maskininlärning**
  * Förbättrad felhantering och dokumentation
  * Prestandaproblem för hämtning av fasta körningsverkningar.
  * Fast fortsätt köra problem.
  * Fasta :::no-loc text="ensembling"::: iterationsproblem.
  * Fast utbildning hängande bugg på MAC OS.
  * Nedsampling makrogenomsnitt PR / ROC kurva i anpassade valideringsscenario.
  * Tog bort extra indexlogik.
  * Tog bort filtret från get_output API.

+ **Pipelines**
  * Lade till en metod Pipeline.publish() för att publicera en pipeline direkt, utan att kräva en körning först.
  * Lade till en metod PipelineRun.get_pipeline_runs() för att hämta pipeline-körningar som genererades från en publicerad pipeline.

+ **Projekt Brainwave**
  * Uppdaterat stöd för nya AI-modeller tillgängliga på FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
[Version 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) innehåller följande funktioner och buggfixar:

+ **Nya funktioner**
  * Stöd för en-het kodning
  * Stöd för kvantiltransformering

+ **Fel åtgärdat:**
  * Fungerar med alla Tornado version, inget behov av att nedgradera din Tornado version
  * Värdet räknas för alla värden, inte bara de tre översta

## <a name="2018-09-public-preview-refresh"></a>2018-09 (Uppdatering av offentlig förhandsversion)

En ny, uppdaterad version av Azure Machine Learning: Läs mer om den här versionen:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](overview-what-is-azure-ml.md).
