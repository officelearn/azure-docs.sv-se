---
title: 'Självstudie: din första ML-modell med R'
titleSuffix: Azure Machine Learning
description: I den här självstudien får du lära dig grundläggande design mönster i Azure Machine Learning och träna en logistik Regressions modell modell med R-paketen azuremlsdk och cirkumflex för att förutse sannolikheten för en oåterkallelighet i en bil haveri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 11/04/2019
ms.openlocfilehash: 52dc0ff27ad2f04b9faeab24c6bdba68d9ec138e
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307272"
---
# <a name="tutorial-train-and-deploy-your-first-model-in-r-with-azure-machine-learning"></a>Självstudie: träna och distribuera din första modell i R med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien får du lära dig grundläggande design mönster i Azure Machine Learning.  Du tränar och distribuerar en **cirkumflex** -modell för att förutsäga sannolikheten för en allvarlig olycka i en bil haveri. När du har slutfört den här självstudien har du erfarenhet av R SDK för att skala upp för att utveckla mer komplexa experiment och arbets flöden.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Anslut din arbets yta
> * Läs in data och Förbered för utbildning
> * Ladda upp data till data lagret så att den är tillgänglig för fjärran sluten utbildning
> * Skapa en beräknings resurs
> * Träna en cirkumflex-modell för att förutsäga sannolikheten för en allvarlighet
> * Distribuera en förutsägelse slut punkt
> * Testa modellen från R

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="prerequisites"></a>Förutsättningar

1. Följ [installations anvisningarna](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) för att:
    + Installera Anaconda
    + Installera `azuremlsdk`
    + Installera Azure Machine Learning SDK för python

1. Hämta de tre själv studie kurs filerna från [GitHub](https://github.com/Azure/azureml-sdk-for-r/tree/master/vignettes/train-and-deploy-with-caret).  Spara dem i en katalog med **självstudier** .

2. Skapa en Azure Machine Learning arbets yta och ladda ned dess konfigurations fil enligt stegen nedan.


### <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i SDK: n. Om du redan har en Azure Machine Learning arbets yta kan du [gå vidare till nästa avsnitt](#config). Annars skapar du en nu.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="config"></a>Ladda ned config. JSON-fil

1. Välj **Hämta config. JSON**överst i arbets ytans sida.

    ![Ladda ned config. JSON-fil](media/tutorial-1st-r-experiment/download-config.png)  

1. Lägg till den här filen i **självstudier** -katalogen.

Nu är du redo att köra själv studie kursen.

Vi rekommenderar att du använder RStudio för att köra den här självstudien. I RStudio väljer du fil > nytt projekt > befintlig katalog och väljer den **själv studie** katalog som du skapade ovan.

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i **träna-and-Deploy-to-ACI. RMD** -fil.  
> Om du har erfarenhet av RMarkdown kan du använda koden från filen.  Eller så kan du kopiera/klistra in kodfragment från där, eller från den här artikeln till ett R-skript eller kommando raden.


## <a name="set-up-your-development-environment"></a>Konfigurera utvecklingsmiljön
Installations programmet för ditt utvecklings arbete i den här självstudien innehåller följande åtgärder:

* Installera de paket som krävs
* Anslut till en arbets yta så att den lokala datorn kan kommunicera med fjär resurser
* Skapa ett experiment för att spåra dina körningar
* Skapa ett fjärrberäknings mål som ska användas för utbildning

### <a name="install-required-packages"></a>Installera de paket som krävs
Den här självstudien förutsätter att du redan har Azure ML SDK installerat. Gå vidare och importera **azuremlsdk** -paketet.

```R
library(azuremlsdk)
```

I självstudien används data från [ **DAAG** -paketet](https://cran.r-project.org/package=DAAG). Installera paketet om du inte har det.

```R
install.packages("DAAG")
```

Utbildnings-och Poäng skripten (`accidents.R` och `accident_predict.R`) har vissa ytterligare beroenden. Om du planerar att köra dessa skript lokalt ser du till att du har de nödvändiga paketen också.

### <a name="load-your-workspace"></a>Läs in din arbets yta
Skapa en instans av ett arbets områdes objekt från din befintliga arbets yta. Följande kod läser in arbets ytans information från **config. JSON** -filen. Du kan också hämta en arbets yta med [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html).

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Skapa ett experiment
Ett Azure ML-experiment spårar en gruppering av körningar, vanligt vis från samma utbildnings skript. Skapa ett experiment för att spåra körningarna för att träna cirkumflexs-modellen på olyckor-data.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål
Genom att använda Azure Machine Learning Compute (AmlCompute), en hanterad tjänst så kan datavetare träna maskininlärningsmodeller i kluster med virtuella Azure-datorer. Exempel innefattar virtuella datorer med GPU-stöd. I den här självstudien skapar du ett AmlCompute-kluster med en nod som utbildnings miljö. I koden nedan skapas beräknings klustret om det inte redan finns på arbets ytan.

Du kan behöva vänta några minuter på att ditt beräknings kluster ska tillhandahållas om det inte redan finns.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Förbereda data för utbildning
I den här självstudien används data från **DAAG** -paketet. Den här data uppsättningen innehåller data från över 25 000 bil krascher i USA, med variabler som du kan använda för att förutsäga sannolikheten för en allvarlighet. Börja med att importera data till R och omvandla dem till en ny dataframe-`accidents` för analys, och exportera dem till en `Rdata` fil.

```R
library(DAAG)
data(nassCDS)

accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Ladda upp data till data lagret
Ladda upp data till molnet så att de kan komma åt den i din miljö för fjärr utbildning. Varje Azure ML-arbetsyta levereras med ett standard data lager som lagrar anslutnings informationen till Azure Blob-behållaren som är etablerad i det lagrings konto som är kopplat till arbets ytan. Följande kod överför de olycks data som du skapade ovan till det data lagret.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Träna en modell

I den här självstudien får du anpassa en logistik Regressions modell på dina överförda data med hjälp av fjärrberäknings klustret. Om du vill skicka ett jobb måste du:

* Förbereda övnings skriptet
* Skapa ett beräkningsobjekt
* Skicka jobbet

### <a name="prepare-the-training-script"></a>Förbereda övnings skriptet
Ett utbildnings skript som heter `accidents.R` har angetts för dig i samma katalog som den här självstudien. Observera följande information **i övnings skriptet** som har utförts för att utnyttja Azure ml-tjänsten för utbildning:

* Övnings skriptet tar ett argument `-d` för att hitta den katalog som innehåller tränings data. När du definierar och skickar jobbet senare pekar du på data lagret för det här argumentet. Azure ML kommer att montera lagringsmappen till fjärrklusteret för utbildnings jobbet.
* Övnings skriptet loggar den slutliga noggrannheten som ett Mät värde för körnings posten i Azure ML med hjälp av `log_metric_to_run()`. Azure ML SDK innehåller en uppsättning loggnings-API: er för att logga olika mått under inlärnings körningar. Dessa mått registreras och behålls i experiment körnings posten. Måtten kan sedan nås när som helst eller visas på sidan körnings information i [Azure Machine Learning Studio](https://ml.azure.com). Se [referensen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) för en fullständig uppsättning loggnings metoder `log_*()`.
* Övnings skriptet sparar din modell i en katalog med namnet **outputs**. `./outputs`-mappen får särskild behandling av Azure ML. Under utbildningen överförs filer som skrivs till `./outputs` automatiskt till din körnings post av Azure ML och behålls som artefakter. Genom att spara den tränade modellen till `./outputs`kommer du att kunna komma åt och hämta modell filen även när körningen är över och du inte längre har åtkomst till din fjärran sluten miljö.

### <a name="create-an-estimator"></a>Skapa ett beräkningsobjekt

En Azure ML-uppskattning kapslar in den körnings konfigurations information som krävs för att köra ett utbildnings skript på beräknings målet. Azure ML-körningar körs som behållare jobb på det angivna beräknings målet. Som standard innehåller Docker-avbildningen som skapats för utbildnings jobbet R, Azure ML SDK och en uppsättning ofta använda R-paket. Se den fullständiga listan med standard paket som ingår här.

Skapa en uppskattning genom att definiera:

* Den katalog som innehåller dina skript som krävs för utbildning (`source_directory`). Alla filer i den här katalogen överförs till klusternoderna för körning. Katalogen måste innehålla ditt utbildnings skript och eventuella ytterligare skript som krävs.
* Det utbildnings skript som ska köras (`entry_script`).
* Compute Target (`compute_target`), i det här fallet det AmlCompute-kluster som du skapade tidigare.
* De parametrar som krävs från övnings skriptet (`script_params`). Azure ML kommer att köra ditt utbildnings skript som ett kommando rads skript med `Rscript`. I den här självstudien anger du ett argument till skriptet, data katalogens monterings punkt, som du kan komma åt med `ds$path(target_path)`.
* Alla miljö beroenden som krävs för träning. Standard Docker-avbildningen som skapats för utbildning innehåller redan de tre paketen (`caret`, `e1071`och `optparse`) som krävs i övnings skriptet.  Så du behöver inte ange ytterligare information. Om du använder R-paket som inte ingår som standard använder du uppskattningens `cran_packages` parameter för att lägga till ytterligare CRAN-paket. Se [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) referens för en fullständig uppsättning konfigurerbara alternativ.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Skicka jobbet till det fjärranslutna klustret

Skicka slutligen jobbet som ska köras i klustret. `submit_experiment()` returnerar ett körnings objekt som du sedan använder för att gränssnitt med körningen. Den första körningen tar totalt **cirka 10 minuter**. Men för senare körningar används samma Docker-avbildning igen så länge skript beroendena inte ändras.  I det här fallet cachelagras avbildningen och behållar start tiden är mycket snabbare.

```R
run <- submit_experiment(exp, est)
```

Du kan visa körnings information i RStudio Viewer. Om du klickar på länken "webbvy" visas Azure Machine Learning Studio, där du kan övervaka körningen i användar gränssnittet.

```R
view_run_details(run)
```

Modell träning sker i bakgrunden. Vänta tills modellen har slutfört träningen innan du kör mer kod.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Du-och-kollegor med åtkomst till arbets ytan – kan skicka flera experiment parallellt, och Azure ML tar hänsyn till schemaläggningen av aktiviteterna i beräknings klustret. Du kan till och med konfigurera klustret så att det automatiskt skalar upp till flera noder och skala tillbaka när det inte finns några fler beräknings aktiviteter i kön. Den här konfigurationen är ett kostnads effektivt sätt för team att dela beräknings resurser.

## <a name="retrieve-training-results"></a>Hämta utbildnings resultat
När din modell har slutfört Utbildningen kan du komma åt artefakterna för jobbet som sparades till körnings posten, inklusive alla mått som loggats och den slutliga tränade modellen.

### <a name="get-the-logged-metrics"></a>Hämta de loggade måtten
I övnings skriptet `accidents.R`loggade du in ett mått från din modell: precisionen för förutsägelserna i tränings data. Du kan se mått i [Studio](https://ml.azure.com)eller extrahera dem till den lokala sessionen som en R-lista enligt följande:

```R
metrics <- get_run_metrics(run)
metrics
```

Om du har kört flera experiment (t. ex. genom att använda olika variabler, algoritmer eller standardvärden) kan du använda måtten från varje körning för att jämföra och välja den modell som du ska använda i produktionen.

### <a name="get-the-trained-model"></a>Hämta den tränade modellen
Du kan hämta den tränade modellen och titta på resultatet i din lokala R-session. Följande kod hämtar innehållet i `./outputs`-katalogen, som innehåller modell filen.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Du ser några faktorer som bidrar till en ökning av den uppskattade sannolikheten för dödsfall:

* högre effekt hastighet 
* hane-drivrutin
* äldre person
* Sand

Du ser lägre sannolikhet för dödsfall med:

* förekomst av Airbags
* närvaro Seatbelts
* främre kollisionen 

Tillverknings året har ingen betydande inverkan.

Du kan använda den här modellen för att skapa nya förutsägelser:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Distribuera som en webbtjänst

Med din modell kan du förutsäga risken för dödsfall från en kollision. Använd Azure ML för att distribuera din modell som en förutsägelse tjänst. I den här självstudien ska du distribuera-webb tjänsten i [Azure Container instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Registrera modellen

Registrera först den modell som du laddade ned till din arbets yta med [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). En registrerad modell kan vara en samling filer, men i det här fallet är R Model-objektet tillräckligt. Azure ML kommer att använda den registrerade modellen för distribution.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definiera beroenden för härledning
Om du vill skapa en webb tjänst för din modell måste du först skapa ett bedömnings skript (`entry_script`), ett R-skript som tar sig som indata-variabel (i JSON-format) och returnerar en förutsägelse från din modell. I den här självstudien använder du den angivna bedömnings filen `accident_predict.R`. Bedömnings skriptet måste innehålla en `init()` metod som läser in din modell och returnerar en funktion som använder modellen för att göra en förutsägelse baserad på indata. Mer information finns i [dokumentationen](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) .

Definiera sedan en Azure ML- **miljö** för ditt skripts paket beroenden. Med en miljö anger du R-paket (från CRAN eller någon annan stans) som behövs för att skriptet ska köras. Du kan också ange värden för miljövariabler som skriptet kan referera till för att ändra dess beteende. Som standard kommer Azure ML att bygga samma standard Docker-avbildning som används med uppskattningen för utbildning. Eftersom självstudien inte har några särskilda krav skapar du en miljö utan särskilda attribut.

```R
r_env <- r_environment(name = "basic_env")
```

Om du vill använda en egen Docker-avbildning för distribution i stället anger du parametern `custom_docker_image`. Se [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) referens för en fullständig uppsättning konfigurerbara alternativ för att definiera en miljö.

Nu har du allt du behöver för att skapa en **konfigurations härledning** för att kapsla in ditt bedömnings skript och miljö beroenden.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Distribuera till ACI
I den här självstudien ska du distribuera tjänsten till ACI. Den här koden tillhandahåller en enda behållare som svarar på inkommande begär Anden, vilket är lämpligt för testning och lätta inläsningar. Se [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) för ytterligare konfigurerbara alternativ. (För distributioner av produktions skala kan du även [distribuera till Azure Kubernetes-tjänsten](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Nu distribuerar du din modell som en webb tjänst. Distributionen **kan ta flera minuter**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testa den distribuerade tjänsten

Nu när din modell distribueras som en tjänst kan du testa tjänsten från R med hjälp av [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html).  Ange en ny data uppsättning att förutse från, konvertera den till JSON och skicka den till tjänsten.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Du kan också hämta webb tjänstens HTTP-slutpunkt, som accepterar REST-klient samtal. Du kan dela den här slutpunkten med alla som vill testa webbtjänsten eller integrera den i ett program.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurserna när du inte längre behöver dem. Ta inte bort någon resurs som du planerar att använda fortfarande. 

Ta bort webb tjänsten:
```R
delete_webservice(aci_service)
```

Ta bort den registrerade modellen:
```R
delete_model(model)
```

Ta bort beräknings klustret:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Ta bort allt

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört ditt första Azure Machine Learning experiment i R kan du läsa mer om [Azure Machine Learning SDK för r](https://azure.github.io/azureml-sdk-for-r/index.html).

