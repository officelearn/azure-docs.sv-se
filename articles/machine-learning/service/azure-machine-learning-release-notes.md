---
title: Vad är nytt i versionen?
titleSuffix: Azure Machine Learning service
description: 'Lär dig mer om de senaste uppdateringarna för Azure Machine Learning service och Machine Learning och data prepare SDK: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 2721e134e03d3d622e61085dc39a2914098ba570
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70930743"
---
# <a name="azure-machine-learning-service-release-notes"></a>Viktig information för Azure Machine Learning-tjänsten

I den här artikeln lär du dig om Azure Machine Learning-tjänstversioner.  Information om fullständiga SDK-referenser finns på Azure Machine Learning huvud sidan [**för SDK för python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens. 

Se [lista över kända problem](resource-known-issues.md) att lära dig om kända fel och lösningar.

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webb miljö (för hands version) för Azure Machine Learning arbets ytor 
Med den nya webb upplevelsen kan data experter och data tekniker slutföra sin slut punkt till slut punkt för Machine Learning-livscykel från förberedelser och visualisera data för att träna och distribuera modeller på en enda plats. 

![Azure Machine Learning arbets ytans användar gränssnitt (för hands version)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Viktiga funktioner:**

Med det här nya Azure Machine Learning-gränssnittet kan du nu:
+ Hantera dina antecknings böcker eller länka ut till Jupyter
+ [Köra automatiserade ML-experiment](tutorial-first-experiment-automated-ml.md)
+ [Skapa data uppsättningar från lokala filer, data lager &-webbfiler](how-to-create-register-datasets.md)
+ Utforska & förbereda data uppsättningar för skapande av modell
+ Övervaka data driften för dina modeller 
+ Visa de senaste resurserna från en instrument panel

I samband med den här versionen stöds följande webbläsare: Chrome, Firefox, Safari och Microsoft Edge Preview.

**Kända problem:**

1. Uppdatera webbläsaren om du ser "något gick fel! Det gick inte att läsa in Chunk-filer när distribution pågår.  

1. Kan inte ta bort eller byta namn på filen i antecknings böcker och filer. Under den offentliga för hands versionen kan du använda Jupyter UI eller terminalen i Notebook VM för att utföra uppdaterings fil åtgärder. Eftersom det är ett monterat nätverks fil system visas alla ändringar du gör på den virtuella datorn för bärbara datorer direkt i arbets ytan Notebook. 

1. För SSH till den virtuella Notebook-datorn:
   1. Hitta de SSH-nycklar som skapades under installationen av den virtuella datorn. Eller Sök efter nycklarna i Azure ML-Azure Portal > Öppna fliken Compute > Leta upp den virtuella Notebook-datorn i listan > öppna dess egenskaper: kopiera nycklarna från dialog rutan.
   1. Importera dessa offentliga och privata SSH-nycklar till den lokala datorn.
   1. Använd dem för SSH i den virtuella Notebook-datorn. 

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK för python v-1.0.60

+ **Nya funktioner**
  + Introducerade FileDataset, som refererar till en eller flera filer i dina data lager eller offentliga URL: er. Filerna kan vara i valfritt format. FileDataset ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Om du vill veta mer om FileDataset https://aka.ms/file-dataset kan du besöka.
  + Stöd för pipeline-yaml har lagts till för PythonScript steg, Adla steg, Databricks steg, DataTransferStep och AzureBatch steg

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-Core**
    + AutoArima är nu en förslags bara pipeline för för hands version.
    + Förbättrad fel rapportering för Prognosticering.
    + Förbättrad loggning genom att använda anpassade undantag i stället för allmän i prognos aktiviteterna.
    + Kontrollen av max_concurrent_iterations har tagits bort till mindre än det totala antalet iterationer.
    + AutoML-modeller returnerar nu AutoMLExceptions
    + Den här versionen förbättrar körnings prestandan för automatiserade lokala Machine Learning-körningar.
  + **azureml-core**
    + Presentera data uppsättning. get _all (arbets yta), som returnerar en `TabularDataset` ord `FileDataset` lista och objekt som har registrerats med registrerings namnet. 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + Presentera `parition_format` som argument till `Dataset.Tabular.from_delimited_files` och `Dataset.Tabular.from_parquet.files`. Partitionsinformation för varje data Sök väg extraheras till kolumner baserat på det angivna formatet. {column_name} skapar en sträng kolumn och {column_name: ÅÅÅÅ/MM/dd/HH/mm/SS} skapar kolumnen DateTime, där ÅÅÅÅ, MM, DD, HH, mm och SS används för att extrahera år, månad, dag, timme, minut och sekund för datum/tid-typen. Partition_format bör starta från positionen för den första partitionen tills fil Sök vägen är slut. Till exempel, med sökvägen ".. /USA/2019/01/01/data.csv "där partitionen är per land och tid, partition_format ="/{Country}/{PartitionDate: ÅÅÅÅ/MM/DD}/data. csv "skapar sträng kolumnen land med värdet" USA "och datetime-kolumnen" PartitionDate "med värdet" 2019-01-01 ".
    + `to_csv_files`och `to_parquet_files` -metoder har lagts till `TabularDataset`i. Dessa metoder möjliggör konvertering mellan a `TabularDataset` och a `FileDataset` genom att konvertera data till filer med det angivna formatet.
    + Logga automatiskt in i bas avbildnings registret när du sparar en Dockerfile som genereras av Model. Package ().
    + ' gpu_support ' behövs inte längre. AzureML identifierar och använder nu NVIDIA Docker-tillägget när det är tillgängligt. Den kommer att tas bort i en framtida version.
    + Stöd har lagts till för att skapa, uppdatera och använda PipelineDrafts.
    + Den här versionen förbättrar körnings prestandan för automatiserade lokala Machine Learning-körningar.
    + Användare kan fråga mått från körnings historik efter namn.
    + Förbättrad loggning genom att använda anpassade undantag i stället för allmän i prognos aktiviteterna.
  + **azureml-explain-model**
    + Feature_maps-parametern har lagts till i den nya MimicWrapper, vilket gör det möjligt för användare att hämta rå funktions förklaringar.
    + Uppladdningar av data uppsättningar är nu inaktiverade som standard för förklarings överföring och kan aktive ras igen med upload_datasets = True
    + Filter parametrarna "is_law" har lagts till i förklarings listan och nedladdnings funktionerna.
    + Lägger till `get_raw_explanation(feature_maps)` metoden i både globala och lokala förklarings objekt.
    + En versions kontroll har lagts till i lightgbm med den utskrivna varningen om lägre version
    + Optimerad minnes användning vid batch-förklaring
    + AutoML-modeller returnerar nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Stöd har lagts till för att skapa, uppdatera och använda PipelineDrafts – kan användas för att underhålla föränderligt-pipeline-definitioner och använda dem interaktivt för att köra
  + **azureml-train-automl**
    + Funktionen har skapats för att installera vissa versioner av GPU-kompatibel pytorch v 1.1.0, CUDA Toolkit 9,0, pytorch-transformatorer, vilket krävs för att aktivera BERT/XLNet i fjärrkörnings miljön för python.
  + **azureml-train-core**
    + Tidigt fel i vissa fel definitions fel i det här området direkt i SDK i stället för på Server sidan.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning data prep SDK v 1.1.14
+ **Fel korrigeringar och förbättringar**
  + Aktiverade skrivning till ADLS/ADLSGen2 med hjälp av rå sökväg och autentiseringsuppgifter.
  + En bugg har åtgärd ATS `include_path=True` som inte fungerade för `read_parquet`.
  + Ett `to_pandas_dataframe()` fast fel orsakades av undantaget "ogiltigt egenskaps värde: hostSecret".
  + Ett fel har åtgärd ATS där filer inte kunde läsas på DBFS i Spark-läge.
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK för python v-1.0.57
+ **Nya funktioner**
  + Aktive rad `TabularDataset` för användning av AutomatedML. Om du vill veta `TabularDataset`mer om kan https://aka.ms/azureml/howto/createdatasets du besöka.
  
+ **Fel korrigeringar och förbättringar**
  + **automl-client-core-nativeclient**
    + Korrigerade felet, utlöses när inlärnings-och/eller verifierings etiketter (y och y_valid) har angetts i formatet Pandas dataframe, men inte som numpy matris.
    + Gränssnittet har uppdaterats för `RawDataContext` att skapa ett för att endast kräva `AutoMLBaseSettings` data och objektet.
    +  Tillåt AutoML-användare att släppa inlärnings serier som inte är tillräckligt långa vid prognoser. – Tillåt AutoML-användare att släppa kärnor från den test uppsättning som inte finns i träningen när du skapar prognoser.
  + **azure-cli-ml**
    + Nu kan du uppdatera SSL-certifikatet för den poängsättnings slut punkt som distribueras i AKS-kluster både för Microsoft-genererade och kund certifikat.
  + **azureml-automl-Core**
    + Ett problem har åtgärd ATS i AutoML där rader med etiketter som saknas har tagits bort felaktigt.
    + Förbättrad fel loggning i AutoML; fullständiga fel meddelanden kommer nu alltid att skrivas till logg filen.
    + AutoML har uppdaterat paket fästen för att `azureml-defaults`inkludera `azureml-explain-model`, och `azureml-dataprep`. AutoML kommer inte längre att varna vid paket matchnings fel (förutom `azureml-train-automl` paket).
    + Ett problem har åtgärd ATS i timeseries, där ka-delningar är av samma storlek och det går inte att utföra lager plats beräkningen.
    + När du kör Ensemble-iteration för inlärnings typen kors validering, om vi har problem med att ladda ned de modeller som har tränats på hela data uppsättningen, hade vi en inkonsekvens mellan modellens vikt och de modeller som har matats in i röstningen Ensemble.
    + Korrigerade felet, utlöses när inlärnings-och/eller verifierings etiketter (y och y_valid) har angetts i formatet Pandas dataframe, men inte som numpy matris.
    + Har åtgärdat problemet med prognos uppgifter när inget har påträffats i de booleska kolumnerna i ingångs tabeller.
    + Tillåt AutoML-användare att släppa inlärnings serier som inte är tillräckligt långa vid prognoser. – Tillåt AutoML-användare att släppa kärnor från den test uppsättning som inte finns i träningen när du skapar prognoser.
  + **azureml-core**
    + Åtgärdat problem med blob_cache_timeout-parameter ordning.
    + Externa anpassade och transformera undantags typer har lagts till i systemfel.
    + Stöd har lagts till för Key Vault hemligheter för fjärrkörningar. Lägg till en azureml. Core. nyckel valvs klass för att lägga till, hämta och lista hemligheter från det nyckel valv som är associerat med din arbets yta. Åtgärder som stöds är:
      + azureml. Core. Workspace. arbetsyte. get _default_keyvault ()
      + azureml. Core. nyckel valv. nyckel valv. Ange _secret (namn, värde)
      + azureml. Core. nyckel valv. nyckel valv. Ange _secrets (secrets_dict)
      + azureml. Core. nyckel valv. Hämta _secret (namn)
      + azureml. Core. nyckel valv. Hämta _secrets (secrets_list)
      + azureml. Core. nyckel valv. list_secrets ()
    + Ytterligare metoder för att hämta standard nyckel valv och få hemligheter under fjärrkörning:
      + azureml. Core. Workspace. arbetsyte. get _default_keyvault ()
      + azureml. Core. Run. Run. get _secret (Name)
      + azureml. Core. Run. Run. get _secrets (secrets_list)
    + Ytterligare parametrar för åsidosättning har lagts till för Submit-HyperDrive CLI-kommandot.
    + Bättre tillförlitlighet för API-anrop för att expandera nya begär Anden biblioteks undantag.
    + Lägg till stöd för att skicka körningar från en skickad körning.
    + Ett problem med en SAS-token med fast förfallo tid i FileWatcher, som gjorde att filer slutade laddas upp efter att deras ursprungliga token hade upphört
    + Import av HTTP CSV/TSV-filer som stöds i data uppsättning python SDK.
    + Föråldrad-metoden arbetsyte. Setup (). Varnings meddelandet som visas för användare föreslår att du använder Create () eller Get ()/from_config () i stället.
    + Miljön har lagts till. Lägg till _private_pip_wheel (), som möjliggör överföring av privata anpassade python-paket (. WHL) till arbets ytan och att använda dem på ett säkert sätt för att bygga/materialisera miljön.
    + Nu kan du uppdatera SSL-certifikatet för den poängsättnings slut punkt som distribueras i AKS-kluster både för Microsoft-genererade och kund certifikat.
  + **azureml-explain-model**
    + Parameter har lagts till för att lägga till ett modell-ID till förklaringar vid uppladdning.
    + Taggar `is_raw` har lagts till i förklaringar i minnet och uppladdning.
    + Stöd för pytorch och tester har lagts till för azureml-förklarar-modell-paketet.
  + **azureml-opendatasets**
    + Stöd för att identifiera och logga automatiska test miljöer.
    + Klasser har lagts till för att hämta befolkning efter region och post.
  + **azureml-pipeline-core**
    + Etikett egenskapen har lagts till i definitionerna för indata och utdata.
  + **azureml – telemetri**
    + En felaktig konfiguration av telemetri har åtgärd ATS.
  + **azureml-train-automl**
    + Ett fel uppstod vid fel vid installationen av fel, det gick inte att logga in "fel"-fältet för installations körningen och därför sparades inte i den överordnade körningen "fel".
    + Ett problem har åtgärd ATS i AutoML där rader med etiketter som saknas har tagits bort felaktigt.
    + Tillåt AutoML-användare att släppa inlärnings serier som inte är tillräckligt långa vid prognoser.
    + Tillåt AutoML-användare att släppa kärnor från test uppsättningen som inte finns i inlärnings uppsättningen vid prognostisering.
    + Nu AutoMLStep passerar genom automl config till Server del för att undvika problem vid ändringar eller tillägg av nya konfigurations parametrar.
    + AutoML data Guardrail finns nu som offentlig för hands version. Användaren ser en data Guardrail-rapport (för klassificerings-och Regressions aktiviteter) efter utbildning och kan också komma åt den via SDK-API.
  + **azureml-train-core**
    + Stöd för Torch 1,2 har lagts till i PyTorch-uppskattningen.
  + **azureml – widgetar**
    + Förbättrade diagram för förvirrings mat ris för klassificerings träning.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning data prep SDK v 1.1.12
+ **Nya funktioner**
  + Listor över strängar kan nu skickas in som inmatade `read_*` metoder.

+ **Fel korrigeringar och förbättringar**
  + Prestanda för `read_parquet` har förbättrats avsevärt vid körning i Spark.
  + Ett problem har åtgärd `column_type_builder` ATS där det inte gick att skapa en enskild kolumn med tvetydiga datum format.

### <a name="azure-portal"></a>Azure Portal
+ **Förhands gransknings funktion**
  + Logg-och utdatafilen är nu tillgängliga för att köra informations sidor. Filerna strömmar uppdateringar i real tid när växling vid förhands visning är aktive rad.
  + Möjligheten att ange kvot på en arbets ytans nivå släpps i för hands versionen. AmlCompute kvoter tilldelas på prenumerations nivå, men nu kan du distribuera den kvoten mellan arbets ytor och tilldela den till en rättvis delning och styrning. Klicka bara på bladet **användning och kvoter** i det vänstra navigerings fältet på arbets ytan och välj fliken **Konfigurera kvoter** . Observera att du måste vara prenumerations administratör för att kunna ange kvoter på arbets ytans nivå eftersom detta är en åtgärd över arbets ytan.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK för python v-1.0.55

+ **Nya funktioner**
  + Token-baserad autentisering stöds nu för anrop som görs till poängsättnings slut punkten som distribueras på AKS. Vi kommer att fortsätta att stödja den aktuella nyckelbaserade autentiseringen och användarna kan använda någon av dessa autentiseringsmekanismer i taget.
  + Möjlighet att registrera en blob-lagring som ligger bakom det virtuella nätverket (VNet) som ett data lager.
  
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-Core**
    + Åtgärdar ett fel där verifierings storleken för ka-delningar är liten och resulterar i dåliga förutsägande eller sanna diagram för regression och Prognosticering.
    + Loggningen av prognos uppgifter på fjärrdatorn körs bättre, och nu tillhandahålls användaren med ett omfattande fel meddelande om körningen misslyckades.
    + Åtgärdat avbrott i timeseries om preprocess-flaggan är true.
    + Det gick inte att utföra några validerings fel meddelanden i prognos data mer åtgärds bara.
    + Minskad minnes förbrukning av AutoML körs genom att släppa och/eller Lazy inläsning av data uppsättningar, särskilt i mellan processens upphämtningar
  + **azureml-contrib-förklara-modell**
    + Flaggan model_task har lagts till för förklaringar som gör att användaren kan åsidosätta standard logiken för automatisk härledning för modell typ
    + Ändringar i widgeten: Installeras automatiskt med contrib, ingen mer nbextension install/enable-support-förklaring med bara global funktions betydelse (tex Permutative)
    + Instrument panels ändringar:-ruta ritas och violin ritas i stället för beeswarm-observation på sammanfattnings sidan – mycket snabbare åter givning av beeswarm-observationer på "Top-k"-skjutreglaget – användbart meddelande som förklarar hur översta-k är beräknade – användbara anpassningsbara meddelanden i stället för diagram när data har inte angetts
  + **azureml-core**
    + Metoden Model. Package () har lagts till för att skapa Docker-avbildningar och Dockerfiles som kapslar in modeller och deras beroenden.
    + Uppdaterade lokala webbtjänster för att acceptera InferenceConfigs som innehåller miljö objekt.
    + Fast Model. register () genererar ogiltiga modeller när "." (för den aktuella katalogen) skickas som parametern model_path.
    + Lägg till Run. submit_child, funktionen speglar experimentet. submit när du anger Kör som överordnad för den skickade underordnade körningen.
    + Stöd för konfigurations alternativ från Model. register i Run. register_model.
    + Möjlighet att köra JAR-jobb på ett befintligt kluster.
    + Stöder nu instance_pool_id-och cluster_log_dbfs_path-parametrar.
    + Stöd har lagts till för användning av ett miljö objekt när en modell distribueras till en WebService. Environment-objektet kan nu anges som en del av InferenceConfig-objektet.
    + Lägg till appinsifht-mappning för nya regioner – västra-väst-usanorracentrala
    + Lade till dokumentation för alla attribut i alla data lager klasser.
    + Parametern blob_cache_timeout har lagts `Datastore.register_azure_blob_container`till i.
    + Save_to_directory-och load_from_directory-metoder har lagts till i azureml. Core. Environment. Environment.
    + Har lagt till kommandona "AZ ml miljö hämtning" och "AZ ml-miljö registrera" i CLI.
    + Miljön har lagts till. Lägg till _private_pip_wheel-metod.
  + **azureml-explain-model**
    + Data uppsättnings spårning har lagts till i förklaringar med data uppsättnings tjänsten (för hands version).
    + Minskad standard grupp storlek vid strömning av globala förklaringar från 10 000 till 100.
    + Flaggan model_task har lagts till för förklaringar som gör att användaren kan åsidosätta standard logiken för automatisk härledning för modell typ.
  + **azureml-mlflow**
    + Åtgärdat fel i mlflow. azureml. build_image där kapslade kataloger ignoreras.
  + **azureml-pipeline-steps**
    + Möjlighet att köra JAR-jobb på befintliga Azure Databricks-kluster har lagts till.
    + Stöd för instance_pool_id och cluster_log_dbfs_path-parametrar har lagts till för steget DatabricksStep.
    + Stöd har lagts till för pipeline-parametrar i DatabricksStep-steget.
  + **azureml-train-automl**
    + Lade till docstrings för de Ensemble-relaterade filerna.
    + Uppdaterade dokument till ett mer lämpligt språk `max_cores_per_iteration` för och`max_concurrent_iterations`
    + Loggningen av prognos uppgifter på fjärrdatorn körs bättre, och nu tillhandahålls användaren med ett omfattande fel meddelande om körningen misslyckades.
    + Tog bort get_data från pipeline automlstep Notebook.
    + Startade support nu i automlstep.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning data prep SDK v 1.1.10

+ **Nya funktioner**
  + Du kan nu begära att köra vissa kontroller (t. ex. histogram, punkt diagram osv.) på vissa kolumner.
  + Ett parallellisera-argument har `append_columns`lagts till i. Om värdet är true kommer data att läsas in i minnet, men körningen körs parallellt. om det här värdet är False kommer körningen att strömmas, men en enkel tråd.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK för python v-1.0.53

+ **Nya funktioner**
  + Automatiserad Machine Learning stöder nu utbildning av ONNX-modeller på fjärrdatorns mål
  + Azure Machine Learning kan nu återuppta utbildningen från en tidigare körnings-, kontroll punkts-eller modell fil.
    + Lär dig hur du [använder uppskattningar för att återuppta inlärningen från en tidigare körning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Fel korrigeringar och förbättringar**
  + **automl-client-core-nativeclient**
    + Åtgärda felet om återfick kolumn typer efter transformeringen (fel länkad); 
    + Tillåt att y_query är en objekt typ som inte innehåller några (s) i början (#459519).
  + **azure-cli-ml**
    + CLI-kommandon "modell distribution" och "tjänst uppdatering" accepterar nu parametrar, konfigurationsfiler eller en kombination av de två. Parametrar har företräde framför attribut i filer.
    + Modell beskrivningen kan nu uppdateras efter registreringen
  + **azureml-automl-Core**
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (aktuell senaste).
    + Lägga till stöd för Nimbus ML-uppskattningar & pipelines som ska användas inom AutoML-uppskattningar.
    + Åtgärda ett fel i urvals proceduren för ensemblen som inte nödvändigt vis växer till den resulterande ensemblen även om poängen var konstant.
    + Aktivera åter användning av vissa featurizations över ka-delningar för prognos uppgifter. Detta påskyndar körningen av installations programmet genom ungefär en faktor på n_cross_validations för dyra featurizations som lags och rullande Windows.
    + Åtgärda ett problem om tiden ligger utanför Pandas-tidsintervallet som stöds. Vi höjer nu en DataException om tiden är mindre än PD. Timestamp. min eller större än PD. Timestamp. Max
    + Med prognostisering kan du nu använda olika frekvenser i träna och test uppsättningar om de kan justeras. Till exempel kan "kvartals vis som börjar i januari" och "kvartals vis start i oktober" justeras.
    + Egenskapen "parametrar" har lagts till i TimeSeriesTransformer.
    + Ta bort gamla undantags klasser.
    + I prognos uppgifter accepterar- `target_lags` parametern nu ett heltals värde eller en lista med heltal. Om heltalet angavs skapas bara en fördröjning. Om en lista anges kommer de unika värdena för lags att tas. target_lags = [1, 2, 2, 4] kommer att skapa lags av en, 2 och 4 perioder.
    + Åtgärda felet om att förlora kolumn typer efter transformeringen (fel länkad);
    + I `model.forecast(X, y_query)`, kan y_query vara en objekt typ som inte innehåller några (s) i början (#459519).
    + Lägg till förväntade värden i automl-utdata
  + **azureml-contrib-datadrift**
    +  Förbättringar av exempel på bärbara datorer, inklusive växla till azureml-OpenData uppsättningar i stället för azureml-contrib-OpenData uppsättningar och prestanda förbättringar när data berikas
  + **azureml-contrib-förklara-modell**
    + Argument för fasta omvandlingar för LIME-förklaringar för rå funktions prioritet i azureml-contrib-Restore-Model-paket
    + tillagda segment till bild förklaringar i bild förklaring för AzureML-contrib-deklarning-modell paket
    + Lägg till scipy-sparse-stöd för LimeExplainer
    + Lägg till batch_size i härmar förklaring när include_local = falskt för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Korrigering för att anropa set_featurizer_timeseries_params (): förutsägelse värde typ ändring och null-kontroll – Lägg till antecknings bok för timeseries upplärda
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (aktuell senaste).
  + **azureml-core**
    + Har lagt till möjligheten att bifoga DBFS-datalager i AzureML CLI 
    + Korrigerade felet med data lager uppladdning där en tom mapp skapas om `target_path` den startas med`/`
    + Fast deepcopy-problem i ServicePrincipalAuthentication.
    + Har lagt till kommandona "AZ ml Environment show" och "AZ ml Environment List" i CLI.
    + Miljöer har nu stöd för att ange en base_dockerfile som ett alternativ till redan skapade base_image.
    + Den oanvända RunConfiguration-inställningen auto_prepare_environment har marker ATS som föråldrad.
    + Modell beskrivningen kan nu uppdateras efter registreringen
    + Bugfix: Modell och avbildning ta bort nu innehåller mer information om hur du hämtar överordnade objekt som är beroende av dem om borttagningen Miss lyckas på grund av ett överordnat beroende.
    + Fast bugg som skrev tom varaktighet för distributioner som inträffar när du skapar en arbets yta för vissa miljöer.
    + Förbättrad arbets yta skapa fel undantag. Så att användarna inte ser "det går inte att skapa arbets ytan. Det gick inte att hitta... " som meddelandet och se i stället det faktiska fel meddelandet.
    + Lägg till stöd för token-autentisering i AKS-webbtjänster. 
    + Lägg `get_token()` till metod `Webservice` till objekt.
    + CLI-stöd har lagts till för hantering av Machine Learning-datauppsättningar.
    + `Datastore.register_azure_blob_container`Om du vill kan du `blob_cache_timeout` använda ett värde (i sekunder) som konfigurerar blobfuses monterings parametrar för att aktivera förfallo tid för cacheminnet för det här data lagret. Standardvärdet är ingen tids gräns, d.v.s. När en BLOB läses, kommer den att stanna kvar i det lokala cacheminnet tills jobbet är klart. De flesta jobb föredrar den här inställningen, men vissa jobb behöver läsa mer data från en stor data uppsättning än vad som får plats på deras noder. För dessa jobb kan du justera den här parametern för att lyckas. Var försiktig när du justerar den här parametern: om du ställer in värdet för lågt kan det leda till dåliga prestanda, eftersom data som används i en epok går ut innan de används igen. Det innebär att alla läsningar görs från Blob Storage (dvs. nätverket) i stället för det lokala cacheminnet, vilket påverkar inlärnings tiderna negativt.
    + Modell beskrivningen kan nu uppdateras korrekt efter registreringen
    + Modell-och bild borttagning innehåller nu mer information om överordnade objekt som är beroende av dem och som gör att borttagningen Miss fungerar
    + Förbättra resursutnyttjande för fjärrkörningar med hjälp av azureml. mlflow.
  + **azureml-explain-model**
    + Argument för fasta omvandlingar för LIME-förklaringar för rå funktions prioritet i azureml-contrib-Restore-Model-paket
    + Lägg till scipy-sparse-stöd för LimeExplainer
    + lagt till en linjär förklaring av form, samt en annan nivå till förklaring av tabell för att förklara linjära modeller
    + för att efterlikna förklaringar i förklara modell bibliotek, fast fel när include_local = falskt för sparse-indata
    + Lägg till förväntade värden i automl-utdata
    + åtgärds prioritet för fast permutation när omvandlings argument har angetts för att hämta funktioner för RAW-funktioner
    + Lägg till batch_size i härmar förklaring när include_local = falskt för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel
    + för modell för förklarande av modeller är fasta blackbox-förklaringar där Pandas dataframe Indatatyp krävs för förutsägelse
    + Ett fel har åtgärd `explanation.expected_values` ATS där skulle ibland returnera ett flyttal i stället för en lista med ett flyttal.
  + **azureml-mlflow**
    + Förbättra prestanda för mlflow. Set _experiment (experiment_name)
    + Åtgärda fel som används av InteractiveLoginAuthentication för mlflow-tracking_uri
    + Förbättra resursutnyttjande för fjärrkörningar med hjälp av azureml. mlflow.
    + Förbättra dokumentationen för azureml-mlflow-paketet
    + Korrigerings fel där mlflow. log _artifacts ("my_dir") sparar artefakter under "my_dir/< artefakt sökvägar >" i stället för "< artefakt-sökvägar >"
  + **azureml-opendatasets**
    + Fäst pyarrow på OpenData uppsättningar till gamla versioner (< 0.14.0) på grund av ett minnes problem som nyligen har introducerats där.
    +  Flytta azureml-contrib-opendataset till azureml-opendataset. – Tillåt att öppna data uppsättnings klasser registreras på AML-arbetsytan och utnyttja AML-datauppsättnings funktionerna sömlöst. -Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.
  + **azureml-pipeline-steps**
    + DBFS data lager stöds nu för indata och utdata i DatabricksStep.
    + Uppdaterad dokumentation för Azure Batch steg med avseende på indata/utdata.
    + I AzureBatchStep ändrades standardvärdet för *delete_batch_job_after_finish* till *True*.
  + **azureml – telemetri**
    +  Flytta azureml-contrib-opendataset till azureml-opendataset. – Tillåt att öppna data uppsättnings klasser registreras på AML-arbetsytan och utnyttja AML-datauppsättnings funktionerna sömlöst. -Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.
  + **azureml-train-automl**
    + Uppdaterad dokumentation på get_output som visar den faktiska retur typen och ger ytterligare information om hur du hämtar nyckel egenskaper.
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (aktuell senaste).
    + Lägg till förväntade värden i automl-utdata
  + **azureml-train-core**
    + Strängar har nu accepterats som beräknings mål för automatisk justering av den automatiska parametern
    + Den oanvända RunConfiguration-inställningen auto_prepare_environment har marker ATS som föråldrad.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning data prep SDK v 1.1.9

+ **Nya funktioner**
  + Stöd har lagts till för att läsa en fil direkt från en http-eller HTTPS-URL.

+ **Fel korrigeringar och förbättringar**
  + Förbättrat fel meddelande vid försök att läsa en Parquet-datauppsättning från en fjärrkälla (som inte stöds för närvarande).
  + En bugg har åtgärd ATS vid skrivning till Parquet-filformat i ADLS gen 2 och uppdatering av ADLS gen 2-behållare i sökvägen.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuellt gränssnitt
+ **För hands versions funktioner**
  + Modulen "kör R-skript" har lagts till i Visual Interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK för python v-1.0.48

+ **Nya funktioner**
  + **azureml-opendatasets**
    + **azureml-contrib-OpenData uppsättningar** är nu tillgängliga som **azureml-opendataset**. Det gamla paketet kan fortfarande fungera, men vi rekommenderar att du använder **azureml-OpenData uppsättningar** för att flytta framåt för bättre funktioner och förbättringar.
    + Med det nya paketet kan du registrera öppna data uppsättningar som data uppsättning i AML-arbetsytan och utnyttja de funktioner som data uppsättningen erbjuder.
    + Den innehåller också befintliga funktioner som att använda öppna data uppsättningar som Pandas/SPARK-dataframes och plats anslutningar för en viss data uppsättning som väder.

+ **För hands versions funktioner**
    + HyperDriveConfig kan nu acceptera pipeline-objekt som en parameter för att stöda en inställning för hel parameter med en pipeline.

+ **Fel korrigeringar och förbättringar**
  + **azureml-train-automl**
    + Korrigerade felet om att förlora kolumn typer efter omvandlingen.
    + Korrigerade felet för att tillåta att y_query är en objekt typ som inte innehåller några (n) i början. 
    + Har åtgärdat problemet i urvals proceduren för Ensemble som inte nödvändigt vis har lett till den resulterande ensemblen även om poängen var konstant.
    + Korrigerade problemet med inställningarna för whitelist_models och blacklist_models i AutoMLStep.
    + Ett problem har åtgärd ATS som förhindrade användningen av för bearbetning när AutoML skulle ha använts i samband med Azure ML-pipelines.
  + **azureml-opendatasets**
    + Flyttade azureml-contrib-opendataset till azureml-opendataset.
    + Tillåtna öppna data uppsättnings klasser kan registreras på AML-arbetsytan och utnyttja AML-datauppsättnings funktionerna sömlöst.
    + Förbättrad NoaaIsdWeather-prestanda i en icke-SPARK-version nämnvärt.
  + **azureml-explain-model**
    + Uppdaterad online-dokumentation för tolknings objekt.
    + Har lagt till batch_size till härmar förklaring när include_local = false för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel.
    + Vi har åtgärdat `explanation.expected_values` problemet där skulle ibland returnera ett flyttal i stället för en lista med ett flyttal.
    + Förväntade värden har lagts till för automl-utdata för härma-förklaringen i förklara modell biblioteket.
    + Fast permutation-funktions prioritet när omvandlings argument har angetts för att få till gång till funktioner för RAW-funktioner.
    + Har lagt till batch_size till härmar förklaring när include_local = false för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel för bibliotek med modell förklaringar.
  + **azureml-core**
    + Möjligheten att bifoga DBFS-datalager har lagts till i AzureML CLI.
    + Åtgärdade problemet med data lager uppladdning där en tom mapp skapas om `target_path` den startas `/`med.
    + Jämförelse av två data uppsättningar har Aktiver ATS.
    + Modell och avbildning ta bort nu innehåller mer information om hur du hämtar överordnade objekt som är beroende av dem om borttagningen Miss lyckas på grund av ett överordnat beroende.
    + Den oanvända RunConfiguration-inställningen har föråldrats i auto_prepare_environment.
  + **azureml-mlflow**
    + Förbättrad resursutnyttjande för fjärrkörningar som använder azureml. mlflow.
    + Förbättrad dokumentation av azureml-mlflow-paketet.
    + Ett problem har åtgärd ATS där mlflow. log _artifacts ("my_dir") skulle spara artefakter under "my_dir/artefakt-sökvägar" i stället för "artefakt sökvägar".
  + **azureml-pipeline-core**
    + Parametern hash_paths för alla pipeline-steg är föråldrad och kommer att tas bort i framtiden. Som standard är innehållet i source_directory hashed (förutom filer som anges i. amlignore eller. gitignore)
    + Fortsätta att förbättra modul-och ModuleStep för att stödja beräknings typer för vissa moduler, inför RunConfiguration-integrering och ytterligare ändringar för att låsa upp deras användning i pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Förbättrad dokumentation med avseende på indata/utdata.
    + AzureBatchStep: Ändrade delete_batch_job_after_finish-standardvärdet till true.
  + **azureml-train-core**
    + Strängar accepteras nu som beräknings mål för automatisk justering av den automatiska inställningen.
    + Den oanvända RunConfiguration-inställningen har föråldrats i auto_prepare_environment.
    + Föråldrade `conda_dependencies_file_path` parametrar `pip_requirements_file_path` och deras prioriterade `pip_requirements_file` `conda_dependencies_file` respektive.
  + **azureml-opendatasets**
    + Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning data prep SDK v 1.1.8

+ **Nya funktioner**
 + Data Ströms objekt kan nu itereras över, vilket genererar en sekvens med poster. Se dokumentationen för `Dataflow.to_record_iterator`.

+ **Fel korrigeringar och förbättringar**
 + Ökad robusthet för nu SDK.
 + Förbättrad hantering av Pandas-DataFrames med kolumn index som inte är strängar.
 + Bättre prestanda för `to_pandas_dataframe` i data uppsättningar.
 + Ett fel har åtgärd ATS där Spark-körningen av data uppsättningar misslyckades vid körning i en miljö med flera noder.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning data prep SDK v 1.1.7

Vi har återställt en ändring som förbättrade prestanda, eftersom det orsakade problem för vissa kunder som använder Azure Databricks. Om du har drabbats av ett problem på Azure Databricks kan du uppgradera till version 1.1.7 med någon av metoderna nedan:
1. Kör det här skriptet för att uppgradera:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Återskapa klustret, som kommer att installera den senaste data förberedelse SDK-versionen.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK för python v-1.0.45

+ **Nya funktioner**
  + Lägg till besluts trädets surrogat modell för att efterlikna en förklaring i azureml-förklarar-modell-paket
  + Möjlighet att ange en CUDA-version som ska installeras på Inferencing-avbildningar. Stöd för CUDA 9,0, 9,1 och 10,0.
  + Information om Azure ML-inlärnings bas avbildningar är nu tillgängliga i [Azure ml-behållare GitHub-lagringsplats](https://github.com/Azure/AzureML-Containers) och [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + CLI-stöd har lagts till för pipeline-schema. Kör "AZ ml pipeline-h" för att lära dig mer
  + Den anpassade Kubernetes namespace parameter har lagts till i distributions konfiguration och CLI av AKS-webbtjänst.
  + Föråldrad hash_paths-parameter för alla steg i pipeline
  + Model. register stöder nu registrering av `child_paths` flera enskilda filer som en enskild modell där parametern används.
  
+ **För hands versions funktioner**
    + Nu kan du välja att spara Conda-och pip-information för mer tillförlitlig serialisering och deserialisering.
    + Fel korrigering för automatisk funktions väljare.
    + Uppdaterade mlflow. azureml. build_image till det nya API: t, korrigerade buggar som exponerats av den nya implementeringen.

+ **Fel korrigeringar och förbättringar**
  + Tog bort paramiko beroende från azureml-Core. De utfasnings varningar som har lagts till i Legacy Compute Target Attach-metoder.
  + Förbättra prestanda för kör. create_children
  + I härma-förklaringar med binära klassificerare, korrigerar du den sannolikhet när sannolikheten för lärare används för skalning av form värden.
  + Förbättrad fel hantering och meddelande för automatisk maskin inlärning. 
  + Korrigerade problemet med upprepnings tids gränsen för automatisk maskin inlärning.
  + Förbättrade omvandlings prestanda för Time-serien för automatisk maskin inlärning.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning data prep SDK v 1.1.6

+ **Nya funktioner**
  + Summerings funktioner har lagts till för`SummaryFunction.TOPVALUES`Top Values ()`SummaryFunction.BOTTOMVALUES`och de lägsta värdena ().

+ **Fel korrigeringar och förbättringar**
  + Avsevärt bättre prestanda för `read_pandas_dataframe`.
  + En bugg har åtgärd ATS som `get_profile()` skulle leda till att ett data flöde som pekar på binärfiler slutar fungera.
  + Exponeras `set_diagnostics_collection()` för att tillåta programmerings aktivering/inaktive ring av telemetri-samlingen.
  + Ändrade beteendet för `get_profile()`. NaN-värden ignoreras nu för min, medelvärde, STD och sum, som justeras med beteendet för Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK för python v-1.0.43

+ **Nya funktioner**
  + Azure Machine Learning erbjuder nu förstklassig support för populära Machine Learning-och Data Analysis Framework-Scikit – lär dig. Med [ `SKLearn` hjälp](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)av uppskattare kan användare enkelt träna och distribuera Scikit – lära sig modeller.
    + Lär dig hur du [kör en inställning för Scikit med hjälp av HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Stöd har lagts till för att skapa ModuleStep i pipelines tillsammans med modul-och ModuleVersion-klasser för att hantera återanvändbara beräknings enheter.
  + ACI-webbtjänster stöder nu permanent scoring_uri via uppdateringar. Scoring_uri kommer att ändras från IP till fullständigt domän namn. DNS-namnets etikett för FQDN kan konfigureras genom att ange dns_name_label på deploy_configuration. 
  + Nya funktioner för automatisk maskin inlärning:
    + STL-upplärda för Prognosticering
    + KMeans-klustring är aktiverat för funktions rensning
  + AmlCompute kvot godkännanden blev bara snabbare! Vi har nu automatiserat processen att godkänna dina kvot begär Anden inom ett tröskelvärde. För ytterligare information om hur kvoter fungerar, lär [du dig hur du hanterar kvoter](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **För hands versions funktioner**
    + Integrering med [MLflow](https://mlflow.org) 1.0.0-spårning via azureml-MLflow-paket ([exempel på bärbara datorer](https://aka.ms/azureml-mlflow-examples)).
    + Skicka Jupyter Notebook som en körning. [API-referens dokumentation](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Offentlig för hands version av [data](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) genom att använda azureml-contrib-datadrift-paketet ([exempel på bärbara datorer](https://aka.ms/azureml-datadrift-example)). Data drift är en av de främsta orsakerna till att modell precisionen försämras med tiden. Det inträffar när data som betjänas på modell i produktion skiljer sig från de data som modellen tränades på. AML data drifts detektor hjälper kunden att övervaka data driften och skickar en avisering när driften har identifierats. 

+ **Större ändringar**

+ **Fel korrigeringar och förbättringar**
  + RunConfiguration load och Save har stöd för att ange en fullständig fil Sök väg med fullständig backend-kompatibilitet för föregående beteende.
  + Cachelagring har lagts till i ServicePrincipalAuthentication, inaktiverat som standard.
  + Aktivera loggning av flera ritytor under samma mått namn.
  + Modell klassen har nu kunnat importeras från azureml. Core (`from azureml.core import Model`).
  + I pipeline- `hash_path` steg är parametern nu föråldrad. Det nya beteendet är att hash fullständig source_directory, förutom filer som anges i. amlignore eller. gitignore.
  + I pipeline-paket har olika `get_all` och `get_all_*` metoder föråldrats till förmån för `list` respektive `list_*`.
  + azureml. Core. Hämta _run kräver inte längre klasser som ska importeras innan den ursprungliga körnings typen returneras.
  + Ett problem har åtgärd ATS där vissa anrop till WebService Update inte utlöser en uppdatering.
  + Tids gränsen för AKS-webbtjänster ska vara mellan 5ms och 300000ms. Maximalt antal tillåtna scoring_timeout_ms för Poäng begär Anden har dragits från 1 min till 5 min.
  + LocalWebservice-objekt har `scoring_uri` nu `swagger_uri` och egenskaper.
  + Flyttade utdata katalog skapar och matar ut katalog uppladdning från användar processen. Körnings historik SDK har Aktiver ATS för körning i alla användar processer. Detta bör lösa vissa synkroniseringsproblem som uppstår i distribuerade utbildnings körningar.
  + Namnet på azureml-loggen som skrivs från användar processens namn kommer nu att innehålla process namn (endast för distribuerad utbildning) och PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning data prep SDK v 1.1.5

+ **Fel korrigeringar och förbättringar**
  + För tolkade datetime-värden som har ett tvåsiffrigt årtals format har intervallet för giltiga år uppdaterats för att matcha Windows-versionen. Intervallet har ändrats från 1930-2029 till 1950-2049.
  + När du `handleQuotedLineBreaks=True` `\r` läser i en fil och anger kommer att behandlas som en ny rad.
  + En bugg har åtgärd ATS `read_pandas_dataframe` som orsakade fel i vissa fall.
  + Bättre prestanda för `get_profile`.
  + Förbättrade fel meddelanden.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning data prep SDK v 1.1.4

+ **Nya funktioner**
  + Du kan nu använda följande funktioner för uttrycks språk för att extrahera och parsa datetime-värden i nya kolumner.
    + `RegEx.extract_record()`extraherar datetime-element till en ny kolumn.
    + `create_datetime()`skapar DateTime-objekt från separata datetime-element.
  + När du `get_profile()`anropar kan du nu se att quantile-kolumner är märkta som (EST.) för att tydligt indikera att värdena är ungefärliga.
  + Du kan nu använda * * globbing när du läser från Azure Blob Storage.
    + otillräcklig.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Felkorrigeringar**
  + Åtgärdade en bugg som är relaterad till läsning av en Parquet-fil från en fjärran sluten källa (Azure Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK för python v-1.0.39
+ **Något**
  + Alternativet Kör konfigurations auto_prepare_environment är föråldrat, där automatisk förberedelse blir standard.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning data prep SDK v 1.1.3

+ **Nya funktioner**
  + Stöd har lagts till för läsning från en PostgresSQL-databas, antingen genom att anropa read_postgresql eller använda ett data lager.
    + Se exempel i instruktions guider:
      + [Data inmatnings antecknings bok](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Data lager Notebook](https://aka.ms/aml-data-prep-datastore-nb)

+ **Fel korrigeringar och förbättringar**
  + Korrigerade problem med kolumn typs konvertering:
  + Konverterar nu en boolesk eller numerisk kolumn till en boolesk kolumn.
  + Fungerar inte nu när du försöker ange en datum kolumn som datum typ.
  + Förbättrade JoinType-typer och tillhör ande referens dokumentation. När du ansluter till två data flöden kan du nu ange en av följande typer av koppling:
    + INGEN, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Förbättrad data typs inferencing för att identifiera fler datum format.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

I Azure Portal kan du nu:
+ Skapa och kör automatiserade ML-experiment 
+ Skapa en Notebook VM för att testa Jupyter-anteckningsböcker eller dina egna.
+ Anpassa avsnittet nytt redigering (förhands granskning) på arbets ytan Machine Learning tjänst, som innehåller automatiserade Machine Learning, visuellt gränssnitt och virtuella datorer som är värdar för bärbara datorer
    + Skapa automatiskt en modell med automatisk maskin inlärning 
    + Använd ett dra och släpp-visuellt gränssnitt för att köra experiment
    + Skapa en Notebook VM för att utforska data, skapa modeller och distribuera tjänster.
+ Real tids diagram och mått uppdatering i kör rapporter och kör informations sidor
+ Uppdaterad fil visare för loggar, utdata och ögonblicks bilder på sidan körnings information.
+ Ny och förbättrad upplevelse för att skapa rapporter på fliken experiment. 
+ Möjlighet att ladda ned config. JSON-filen från sidan Översikt i arbets ytan Azure Machine Learning service har lagts till.
+ Stöd Machine Learning tjänst arbets yta skapas från Azure Databricks arbets yta 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK för python v-1.0.33
+ **Nya funktioner**
  + _Arbets ytan. Create_ -metoden accepterar nu standardkonfigurationer för kluster för processor-och GPU-kluster.
  + Om det inte går att skapa arbets ytan rensas beroende resurser.
  + Standard Azure Container Registry SKU växlades till Basic.
  + Azure Container Registry skapas Lazy, när det behövs för att skapa eller skapa bilder.
  + Stöd för miljöer för utbildnings körningar.

### <a name="notebook-virtual-machine"></a>Virtuell dator för Notebook 

Använd en Notebook VM som en säker, företags färdig värd miljö för Jupyter-anteckningsböcker där du kan köra maskin inlärnings experiment, distribuera modeller som webb slut punkter och utföra alla andra åtgärder som stöds av Azure Machine Learning SDK med python. Det ger flera funktioner:
+ [Skapa snabbt en förkonfigurerad virtuell](tutorial-1st-experiment-sdk-setup.md) dator med den senaste versionen av Azure Machine Learning SDK och relaterade paket.
+ Åtkomst skyddas via beprövad teknik, till exempel HTTPS, Azure Active Directory autentisering och auktorisering.
+ Tillförlitlig moln lagring med antecknings böcker och kod i ditt Azure Machine Learning-arbetsyta Blob Storage-konto. Du kan på ett säkert sätt ta bort den virtuella Notebook-datorn utan att förlora ditt arbete.
+ Förinstallerade exempel antecknings böcker för att utforska och experimentera med Azure Machine Learning service-funktioner.
+ Fullständiga anpassnings funktioner för virtuella Azure-datorer, valfri VM-typ, alla paket och eventuella driv rutiner. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK för python v-1.0.33 har släppts.

+ Azure ML Maskinvaruaccelererade modeller på [FPGAs](concept-accelerate-with-fpgas.md) är allmänt tillgängligt.
  + Nu kan du [använda paketet azureml-acceleration-Models](how-to-deploy-fpga-web-service.md) för att:
    + Träna vikterna för ett djup neurala-nätverk som stöds (ResNet 50, ResNet 152, DenseNet-121, VGG-16 och SSD-VGG)
    + Använda transfer Learning med de DNN som stöds
    + Registrera modellen med Modellhantering tjänst och Använd modellen
    + Distribuera modellen till en virtuell Azure-dator med en FPGA i ett Azure Kubernetes service-kluster (AKS)
  + Distribuera behållaren till en [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server enhet
  + Betygs ätt dina data med gRPC-slutpunkten med det här [exemplet](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

+ Funktion som sveper för att aktivera dynamiskt tillägg av featurizers för prestanda optimering. Ny featurizers: arbets inbäddningar, vikt för bevis, mål kodning, kodning av text mål, kluster avstånd
+ Smart CV för hantering av träna/giltig Split i automatiserad ML
+ Få minnes optimerings ändringar och förbättringar av körnings prestanda
+ Prestanda förbättring i modell förklaring
+ ONNX modell konvertering för lokal körning
+ Stöd för under sampling har lagts till
+ Intelligent stopp när inga avslutnings kriterier har definierats
+ Staplade ensembler

+ Prognosticering för tids serier
  + Ny predict prediktion-funktion   
  + Nu kan du använda kors validering av rullande ursprung i Time Series-data
  + Nya funktioner som har lagts till för att konfigurera Time Series-lags 
  + Nya funktioner som har lagts till för att stödja löpande fönster mängd funktioner
  + Ny helgdags identifiering och upplärda när lands koden definieras i experiment inställningarna

+ Azure Databricks
  + Aktive rad tids serie prognoser och modell explainabilty/tolknings kapacitet
  + Du kan nu avbryta och återuppta (fortsätta) automatiserade ML-experiment
  + Stöd har lagts till för multicore-bearbetning

### <a name="mlops"></a>MLOps
+ **Lokal distribution & fel sökning för Poäng behållare**<br/> Nu kan du distribuera en ML-modell lokalt och iterera snabbt på bedömnings filen och beroenden för att se till att de fungerar som förväntat.

+ **Introducerade InferenceConfig & Model. Deploy ()**<br/> Modell distribution har nu stöd för att ange en källmapp med ett Entry-skript, samma som en RunConfig.  Dessutom har modell distributionen förenklats till ett enda kommando.

+ **Git-referens spårning**<br/> Kunder har begärt grundläggande funktioner för git-integrering under en viss tid eftersom det hjälper till att upprätthålla en gransknings historik från slut punkt till slut punkt. Vi har implementerat spårning över större entiteter i Azure ML för git-relaterade metadata (lagrings platsen, commit, Clean State). Den här informationen samlas in automatiskt av SDK och CLI.

+ **Modell profilering & validerings tjänst**<br/> Kunderna har ofta svårt att på rätt sätt anpassa den beräkning som är associerad med deras härlednings tjänst. Med vår modell profilerings tjänst kan kunden tillhandahålla exempel indata och vi ska profilera över 16 olika processor-/minnes konfigurationer för att fastställa optimal storlek för distribution.

+ **Ta med din egen bas avbildning för att få en härledning**<br/> En annan vanlig klagomål var svårt att flytta från experimentering till att dela om beroenden. Med vår nya bas avbildnings delnings funktion kan du nu återanvända dina bas avbildningar, beroenden och alla, för att kunna hantera störningar. Detta bör påskynda distributionen och minska mellanrummet från insidan till den yttre slingan.

+ **Förbättrad generering av Swagger-scheman**<br/> Vår tidigare generations Swagger-metod var fel känslig och omöjlig att automatisera. Vi har ett nytt infogat sätt att generera Swagger-scheman från valfri python-funktion via dekoratörer. Vi har öppen källkod för den här koden och vårt schema generations protokoll är inte kopplat till Azure ML-plattformen.

+ **Azure ML CLI är allmänt tillgänglig (GA)**<br/> Modeller kan nu distribueras med ett enda CLI-kommando. Vi har en gemensam feedback från kunden att ingen distribuerar en ML-modell från en Jupyter Notebook. [**Referens dokumentationen för cli**](https://aka.ms/azmlcli) har uppdaterats.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK för python v-1.0.30 har släppts.

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) Introducerades för att lägga till en ny version av en publicerad pipeline samtidigt som du behåller samma slut punkt.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning data prep SDK v 1.1.2

Obs! Data prepare för prepare installation och `numpy` `pandas` paket kommer inte längre att installeras. Se [uppdaterade installations anvisningar](https://aka.ms/aml-data-prep-installation).

+ **Nya funktioner**
  + Nu kan du använda Pivot-transformeringen.
    + Instruktions guide: [Pivot-anteckningsbok](https://aka.ms/aml-data-prep-pivot-nb)
  + Du kan nu använda reguljära uttryck i inbyggda funktioner.
    + Exempel:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Nu kan du använda `to_upper`  och `to_lower` Functionsiuttrycksspråk. 
  + Nu kan du se antalet unika värden för varje kolumn i en data profil.
  + För några av de ofta använda läsar stegen kan du nu skicka `infer_column_types` argumentet. Om den är inställd på `True`försöker data prepare att identifiera och automatiskt konvertera kolumn typer.
    + `inference_arguments`är nu föråldrad.
  + Nu kan du anropa `Dataflow.shape`.

+ **Fel korrigeringar och förbättringar**
  + `keep_columns` accepterar nu ett ytterligare valfritt argument `validate_column_exists`som kontrollerar om resultatet av `keep_columns` ska innehålla kolumner.
  + Alla läsar steg (som läses från en fil) accepterar nu ytterligare ett valfritt `verify_exists`argument.
  + Bättre prestanda vid läsning från Pandas dataframe och hämtning av data profiler.
  + Ett fel har åtgärd ATS där segmentering av ett enskilt steg från ett data flöde misslyckades med ett enda index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Nu kan du skicka om ett befintligt skript som körs på ett befintligt fjärrberäknings kluster. 
  + Nu kan du köra en publicerad pipeline med nya parametrar på fliken pipeliner. 
  + Körnings informationen stöder nu ett nytt visnings program för Snapshot-filen. Du kan visa en ögonblicks bild av katalogen när du skickade en speciell körning. Du kan också hämta den antecknings bok som har skickats för att starta körningen.
  + Du kan nu avbryta överordnade körningar från Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK för python v-1.0.23

+ **Nya funktioner**
  + Azure Machine Learning SDK stöder nu python 3,7.
  + Azure Machine Learning DNN-uppskattningar har nu inbyggt stöd för flera versioner. Till exempel `TensorFlow`  kan en uppskattning nu acceptera en `framework_version` parameter och användarna kan ange version 1,10 eller 1,12. Om du vill ha en lista över de versioner som stöds av din aktuella `get_supported_versions()` SDK-version, kan du anropa den önskade `TensorFlow.get_supported_versions()`Ramverks klassen (till exempel).
  En lista över de versioner som stöds av den senaste SDK-versionen finns i [dokumentationen för DNN-uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning data prep SDK v 1.1.1

+ **Nya funktioner**
  + Du kan läsa flera data lager-/DataPath/DataReference-källor med read_ * transformationer.
  + Du kan utföra följande åtgärder för kolumner för att skapa en ny kolumn: Division, våning, modulo, ström, längd.
  + Data prep är nu en del av Azure ML Diagnostics Suite och loggar diagnostikinformation som standard.
    + Om du vill inaktivera detta ställer du in miljövariabeln på sant: DISABLE_DPREP_LOGGER

+ **Fel korrigeringar och förbättringar**
  + Förbättrad kod dokumentation för ofta använda klasser och funktioner.
  + Ett fel har åtgärd ATS i auto_read_file som inte kunde läsa Excel-filer.
  + Alternativet har lagts till för att skriva över mappen i read_pandas_dataframe.
  + Förbättrad prestanda för dotnetcore2-beroende installation och ytterligare stöd för Fedora 27/28 och Ubuntu 1804.
  + Bättre prestanda vid läsning från Azure-blobbar.
  + Kolumn typs identifiering stöder nu kolumner av typen Long.
  + Ett fel har åtgärd ATS där vissa datum värden visades som tidsstämplar i stället för python DateTime-objekt.
  + Ett fel har åtgärd ATS där vissa antal typer visas som dubbla i stället för heltal.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK för python v-1.0.21

+ **Nya funktioner**
  + Metoden *azureml. Core. Run. create_children* gör det möjligt att skapa flera underordnade körningar under låg latens med ett enda anrop.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning data prep SDK v 1.1.0

+ **Större ändringar**
  + Konceptet för data förberedelse paketet är inaktuell och stöds inte längre. I stället för att spara flera data flöden i ett paket kan du behålla data flöden individuellt.
    + Instruktions guide: [Öppnar och sparar data flöden Notebook](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nya funktioner**
  + I förberedelse av data kan du nu identifiera kolumner som matchar en viss semantisk typ och dela detta. Den STypes som stöds för närvarande är: e-postadress, geografiska koordinater (latitud & longitud), IPv4-och IPv6-adresser, telefonnummer och AMERIKANSKt post nummer.
    + Instruktions guide: [Bärbar typ av semantisk typ](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data prepare stöder nu följande åtgärder för att generera en resulterande kolumn från två numeriska kolumner: subtrahera, multiplicera, dividera och modulo.
  + Du kan anropa `verify_has_data()` ett data flöde för att kontrol lera om data flödet skulle producera poster om det körts.

+ **Fel korrigeringar och förbättringar**
  + Nu kan du ange antalet lager platser som ska användas i ett histogram för numeriska kolumn profiler.
  + `read_pandas_dataframe` Transformeringen kräver nu att DataFrame har namn som är sträng-eller byte-typ.
  + Ett fel har åtgärd ATS `fill_nulls` i transformeringen, där värdena inte fylldes i korrekt om kolumnen saknades.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK för python v-1.0.18

 + **Något**
   + Azureml-tensorboard-paketet ersätter azureml-contrib-tensorboard.
   + I den här versionen kan du konfigurera ett användar konto på ditt hanterade beräknings kluster (amlcompute) medan du skapar det. Detta kan göras genom att de här egenskaperna skickas i konfigurationen. Du hittar mer information i [referens dokumentationen för SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning data prep SDK v 1.0.17

+ **Nya funktioner**
  + Har nu stöd för att lägga till två numeriska kolumner för att generera en resulterande kolumn med uttrycks språket.

+ **Fel korrigeringar och förbättringar**
  + Förbättrad dokumentation och parameter kontroll för random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning data prep SDK v 1.0.16

+ **Fel korrigering**
  + Åtgärdat problem med tjänstens huvud namn som orsakades av en API-ändring.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK för python v-1.0.17

+ **Nya funktioner**

  + Azure Machine Learning har nu det första klass stödet för populär DNN Framework-kedja. Användning [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) av klass användare kan enkelt träna och distribuera kedje modeller.
    + Lär dig hur du [Kör distribuerad utbildning med ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Lär dig hur du kör en inställning för min [parameter med en kedja med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines har lagt till möjlighet att utlösa en pipeline-körning baserat på data lager ändringar. Antecknings [boken](https://aka.ms/pl-schedule) för pipeline-schemat uppdateras för att visa den här funktionen.

+ **Fel korrigeringar och förbättringar**
  + Vi har lagt till stöd Azure Machine Learning pipelines för att ställa in egenskapen source_directory_data_store på ett önskat data lager (till exempel en blob-lagring) på [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) som anges för [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Som standard använder Azure File Store som lagrings data lager, som kan köra begränsnings problem när ett stort antal steg körs samtidigt.

### <a name="azure-portal"></a>Azure Portal

+ **Nya funktioner**
  + Ny dra och släpp tabell redigerings miljö för rapporter. Användare kan dra en kolumn från en källa till tabell området där en förhands granskning av tabellen kommer att visas. Kolumnerna kan ordnas om.
  + Nya loggar fil visare
  + Länkar till experiment körningar, Compute, modeller, avbildningar och distributioner från fliken aktiviteter

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning data prep SDK v 1.0.15

+ **Nya funktioner**
  + Data prepare stöder nu att du skriver fil strömmar från ett data flöde. Ger också möjlighet att ändra fil Ströms namnen för att skapa nya fil namn.
    + Instruktions guide: [Arbeta med fil Ströms Notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Fel korrigeringar och förbättringar**
  + Förbättrad prestanda för t-Digest på stora data mängder.
  + Data prepare stöder nu läsning av data från en DataPath.
  + En snabb kodning fungerar nu på booleska och numeriska kolumner.
  + Andra fel korrigeringar för övriga fel.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK för python v-1.0.15

+ **Nya funktioner**
  + Azure Machine Learning pipelines har lagt till AzureBatchStep ([Notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (Notebook) och tidsbaserad schemaläggnings funktion ([Notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep har uppdaterats för att fungera med Azure SQL Server och Azure Database för PostgreSQL ([Notebook](https://aka.ms/pl-data-trans)).

+ **Något**
  + Föråldrad till förmån `PublishedPipeline.get`för. `PublishedPipeline.get_published_pipeline`
  + Föråldrad till förmån `Schedule.get`för. `Schedule.get_schedule`

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning data prep SDK v 1.0.12

+ **Nya funktioner**
  + Data prepare stöder nu läsning från en Azure SQL-databas med hjälp av data lager.
 
+ **Något**
  + Bättre minnes prestanda för vissa åtgärder på stora data.
  + `read_pandas_dataframe()`måste `temp_folder` nu anges.
  + Egenskapen är inaktuell, används `column_name` i stället. `ColumnProfile` `name`

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK för python v-1.0.10

+ **Ändringar**: 
  + Azure ML SDK har inte längre Azure-CLI-paket som beroende. Mer specifikt har Azure-CLI-Core-och Azure-CLI-profil beroenden tagits bort från azureml-Core. Detta är ändringar som påverkar användaren:
    + Om du utför "AZ-inloggning" och sedan använder azureml-SDK, kommer SDK att göra webbläsaren eller enhets kod loggen en längre tid. Det använder inte några autentiseringsuppgifter som skapats av "AZ login".
    + För Azure CLI-autentisering, t. ex. med "AZ login", använder du klassen _azureml. Core. Authentication. AzureCliAuthentication_ . För Azure CLI-autentisering _installerar du pip Azure-CLI_ i python-miljön där du har installerat azureml-SDK.
    + Om du gör "AZ-inloggning" med ett huvud namn för tjänsten för Automation rekommenderar vi att du använder _azureml. Core. Authentication. ServicePrincipalAuthentication_ -klassen, som azureml-SDK använder inte autentiseringsuppgifter som skapats av Azure CLI. 

+ **Fel korrigeringar**: Den här versionen innehåller oftast mindre fel korrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning data prep SDK v 1.0.8

+ **Felkorrigeringar**
  + Bättre prestanda för att hämta data profiler.
  + Fasta mindre buggar som rör fel rapportering.
  
### <a name="azure-portal-new-features"></a>Azure-portalen: nya funktioner
+ Ny dra och släpp diagram upplevelse för rapporter. Användare kan dra en kolumn eller ett attribut från det till diagram området där systemet automatiskt väljer en lämplig diagram typ för användaren baserat på typen av data. Användare kan ändra diagram typ till andra tillämpliga typer eller lägga till ytterligare attribut.

    Diagram typer som stöds:
    - Linjediagram
    - Histogram
    - Liggande stapeldiagram
    - Låddiagram
    - Punktdiagram
    - Bubbeldiagram
+ Portalen genererar nu dynamiskt rapporter för experiment. När en användare skickar en körning till ett experiment genereras en rapport automatiskt med de inloggade måtten och graferna för att tillåta jämförelse mellan olika körningar. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK för python v-1.0.8

+ **Fel korrigeringar**: Den här versionen innehåller oftast mindre fel korrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning data prep SDK v 1.0.7

+ **Nya funktioner**
  + Data lager förbättringar (dokumenterade i [data lagret instruktions](https://aka.ms/aml-data-prep-datastore-nb)instruktioner)
    + Ytterligare möjlighet att läsa från och skriva till Azure-filresursen och ADLS-datalager i skalar.
    + När du använder data lager, stöder nu data förberedelse med tjänstens huvud namns autentisering i stället för interaktiv autentisering.
    + Stöd har lagts till för wasb-och wasbs-URL: er.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning data prep SDK v 1.0.6

+ **Felkorrigeringar**
  + Åtgärdat fel vid läsning från offentliga läsbara Azure Blob-behållare i Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK för python v-1.0.6
+ **Fel korrigeringar**: Den här versionen innehåller oftast mindre fel korrigeringar

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning data prep SDK v 1.0.4

+ **Nya funktioner**
  + `to_bool`funktionen tillåter nu att felmatchade värden konverteras till fel värden. Detta är den nya standard avvikelsen för `to_bool` och `set_column_types`, medan det tidigare standard beteendet var att konvertera felmatchade värden till false.
  + Vid anrop `to_pandas_dataframe`finns det ett nytt alternativ för att tolka null/saknade värden i numeriska kolumner som Nan.
  + Möjlighet att kontrol lera retur typen för vissa uttryck har lagts till för att säkerställa typ konsekvens och misslyckade tidiga.
  + Nu kan du anropa `parse_json` att parsa värden i en kolumn som JSON-objekt och expandera dem till flera kolumner.

+ **Felkorrigeringar**
  + En bugg har åtgärd ATS som `set_column_types` kraschade i python-3.5.2.
  + En bugg har åtgärd ATS som kraschade vid anslutning till data lagret med en AML-avbildning.

+ **Uppdateringar**
  * [Exempel på bärbara datorer](https://aka.ms/aml-data-prep-notebooks) för att komma igång med självstudier, fallstudier och instruktions guider.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Allmän tillgänglighet

Azure Machine Learning-tjänsten är nu allmänt tillgänglig.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-beräkning
I den här versionen får vi presentera en ny hanterad beräknings upplevelse genom [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). Detta beräknings mål ersätter Azure Batch AI beräkning för Azure Machine Learning. 

Detta Compute-mål:
+ Används för modell utbildning och batch-härledning/Poängsättning
+ Är en engångs beräkning med flera noder
+ Hanterar kluster hantering och jobb schemaläggning för användaren
+ Autoskalning som standard
+ Stöd för både CPU-och GPU-resurser 
+ Möjliggör användning av virtuella datorer med låg prioritet för minskad kostnad

Beräkning av Azure Machine Learning kan skapas i Python, med hjälp av Azure-portalen eller CLI. Den måste skapas i regionen för din arbetsyta och går inte att ansluta till en annan arbetsyta. Detta beräknings mål använder en Docker-behållare för din körning och paketerar dina beroenden för att replikera samma miljö över alla noder.

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
  * to_spark_dataframe () kommer inte längre att krascha när `Kryo` Spark använder serialisering
  * Antal värden Inspector kan nu visa fler än 1000 unika värden
  * Slumpmässig dela misslyckas inte längre om det ursprungliga dataflödet saknar ett namn  

+ **Mer information**
  * [Azure Machine Learning-SDK för dataförberedelse](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs och bärbara datorer
+ ML-Pipelines
  + Nya och uppdaterade anteckningsböcker för att komma igång med pipelines, batch omfång och format överföra exempel: https://aka.ms/aml-pipeline-notebooks
  + Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md)
  + Lär dig hur du [kör batch förutsägelser med pipelines](how-to-run-batch-predictions.md)
+ Azure Machine Learning Compute Target
  + Nu har [exempel antecknings böcker](https://aka.ms/aml-notebooks) uppdaterats för att använda den nya hanterade beräkningen.
  + [Lär dig mer om den här beräkning](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-portalen: nya funktioner
+ Skapa och hantera [beräkning av Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) typer i portalen.
+ Övervaka kvotanvändning och [begär kvot](how-to-manage-quotas.md) för beräkning av Azure Machine Learning.
+ Visa Azure Machine Learning beräknings kluster status i real tid.
+ Virtual network-stöd har lagts till för att skapa en beräkning av Azure Machine Learning och Azure Kubernetes Service.
+ Kör de publicerade pipelinerna igen med befintliga parametrar.
+ Ny [automatiserad machine learning diagram](how-to-understand-automated-ml.md) för klassificering modeller (lift, vinster, kalibrering, funktionen vikten diagram med modellen explainability) och regressionsmodeller (restbelopp och vikten funktionsdiagram med modellen explainability). 
+ Pipelines kan ses i Azure-portalen




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning-SDK för Python v0.1.80

+ **Större ändringar** 
  * *azureml.Train.widgets* namnområde har flyttats till *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* gör blir föråldrat följande klasser - *azureml.core.compute.BatchAICompute* och *azureml.core.compute.DSVMCompute*. Klassen senare tas bort i kommande versioner. Klassen AmlCompute har nu en enklare definition bara behöver en vm_size och max_nodes och skalar automatiskt ditt kluster från 0 till max_nodes när ett jobb skickas. Våra [exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) har uppdaterats med den här informationen och ska ge dig exempel på användning. Vi hoppas att du som den här förenkling av distribution och mycket mer spännande funktioner kommer i en senare version!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Förbered SDK v0.5.1 

Läs mer om Data Prep SDK genom att läsa [referera till docs](https://aka.ms/data-prep-sdk).
+ **Nya funktioner**
   * Skapa en ny DataPrep CLI för att köra paket för förberedelse av data och visa data för en datauppsättning eller dataflöde
   * Omarbetad SetColumnType API för att förbättra användbarhet
   * Omdöpt smart_read_file till auto_read_file
   * Innehåller nu skeva och datamängds i profilen för Data
   * Kan ta prov med stratified sampling
   * Kan läsa från zip-filer som innehåller CSV-filer
   * Kan dela data uppsättnings rader med slumpmässig delning (till exempel i test-träna-uppsättningar)
   * Kan hämta alla kolumn data typer från ett data flöde eller en data profil genom att anropa`.dtypes`
   * Kan hämta rad antalet från ett data flöde eller en data profil genom att anropa`.row_count`

+ **Felkorrigeringar**
   * Fast länge till dubbla konvertering 
   * Fast assert när någon Lägg till kolumn 
   * Ett problem har åtgärdats med FuzzyGrouping, där den inte kan identifiera grupper i vissa fall
   * Fast sorteringsfunktionen respekterar sorteringsordning för flera kolumner
   * Fasta och/eller uttryck liknar hur `pandas` hanterar dem
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
  * \* Arbets yta. compute_targets, data lager, experiment, bilder, modeller och *WebService* är egenskaper i stället för metoder. Ersätt till exempel *Workspace.compute_targets()* med *Workspace.compute_targets*.
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
  * 64-bit positiva heltals spill hanteras nu korrekt i Linux
  * Fast felaktig textetikett för filer med oformaterad text i smart_read
  * Sträng kolumntyp nu visas i måttvyn
  * Antal löses nu att visa ValueKinds som mappats till enkel FieldType i stället för enskilda finns
  * Write_to_csv misslyckas inte längre när sökväg har angetts som en sträng
  * När du använder Ersätt misslyckas lämnar ”hitta” tom inte längre 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning-SDK för Python v0.1.68

+ **Nya funktioner**
  * Stöd för flera innehavare när du skapar en ny arbets yta.

+ **Korrigerade buggar**
  * Du behöver inte längre fästa pynacl-bibliotekets version när du distribuerar webb tjänsten.

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
  * Jobben rapporterar nu rätt status även om de avslutas med en annan felkod än 0. 
  * RunConfig attributet validering i SDK. 
  * HyperDrive kör objektet stöder Avbryt liknar en vanlig körning: du behöver inte vidarebefordra alla parametrar. 
  * Widget förbättringar för att underhålla tillståndet för listrutan värden för distribuerade körningar och HyperDrive körs. 
  * TensorBoard och andra loggar som stöd för filer som fasta för parametern-server. 
  * Intel(R) MPI stöd på serversidan. 
  * Bugfix till parametern justering för distribuerade kör korrigering vid verifiering av i BatchAI. 
  * Kontext Manager identifierar nu den primära instansen. 

+ **Azure-portalen**
  * log_table() och log_row() stöds i informationen om körningen. 
  * Skapa automatiskt diagram för tabeller och rader med 1, 2 eller 3 numeriska kolumner och en valfri kategoriska-kolumn.

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
  * En metod PipelineRun. get _pipeline_runs () har lagts till för att hämta pipeline-körningar som har genererats från en publicerad pipeline.

+ **Project Brainwave**
  * Uppdaterade stöd för nya AI-modeller som finns på FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Förbered SDK v0.2.0
[Version 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) innehåller följande funktioner och fel korrigeringar:

+ **Nya funktioner**
  * Stöd för en frekvent kodning
  * Stöd för quantile transformering
   
+ **Bugg har åtgärdats:**
  * Fungerar med en storm-version du behöver inte nedgradera din storm-version
  * Antal värden för alla värden, inte bara de tre överst

## <a name="2018-09-public-preview-refresh"></a>2018-09 (offentlig förhandsversion uppdatera)

En ny, uppdaterad version av Azure Machine Learning: Läs mer om den här versionen: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning-tjänsten](../service/overview-what-is-azure-ml.md).
