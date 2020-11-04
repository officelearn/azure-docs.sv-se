---
title: 'Självstudie: Använd R för att skapa en Machine Learning-modell (för hands version)'
titleSuffix: Azure Machine Learning
description: I den här självstudien använder du Azure Machine Learning R SDK för att skapa en logistik Regressions modell som förutsäger sannolikheten för en allvarlig olycks händelse i en Car-olycka.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 0e94288b49cd57b59c126c95ca507477f1c56946
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321519"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>Självstudie: Använd R för att skapa en Machine Learning-modell (för hands version)


> [!IMPORTANT]
> Azure Machine Learning R SDK är för närvarande en offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här självstudien använder du Azure Machine Learning R SDK (för hands version) för att skapa en logistik Regressions modell som förutsäger sannolikheten för en allvarlig olycks händelse i en Car-olycka. Du får se hur Azure Machine Learning moln resurser fungerar med R för att tillhandahålla en skalbar miljö för utbildning och distribution av en modell.  

I den här självstudien utför du följande åtgärder:
> [!div class="checklist"]
> * Skapa en Azure Machine Learning-arbetsyta
> * Öppna RStudio från din arbets yta
> * Klona https://github.com/Azure/azureml-sdk-for-r de filer som krävs för att köra den här självstudien i din arbets yta
> * Läs in data och Förbered för utbildning
> * Ladda upp data till ett data lager så att det är tillgängligt för fjärran sluten utbildning
> * Skapa en beräknings resurs för att träna modellen på distans
> * Träna en `caret` modell för att förutsäga sannolikheten för en allvarlighet
> * Distribuera en förutsägelse slut punkt
> * Testa modellen från R

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.


## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten. 

Det finns många [sätt att skapa en arbets yta](how-to-manage-workspace.md). I den här självstudien skapar du en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 


## <a name="open-rstudio"></a><a name="open"></a>Öppna RStudio

I det här exemplet används en beräknings instans i din arbets yta för en installations fri och förkonfigurerad upplevelse. Använd [din egen miljö](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) om du vill ha kontroll över din miljö, paket och beroenden på din egen dator.

Använd RStudio på en beräknings instans i Azure ML för att köra den här självstudien.  

1. Välj **Beräkna** till vänster.

1. Lägg till en beräknings resurs om det inte redan finns en.

1. När beräkningen har körts använder du **RStudio** -länken för att öppna RStudio.


## <a name="clone-the-sample-vignettes"></a><a name="azure"></a>Klona exempel vignettes 

Klona https://github.com/Azure/azureml-sdk-for-r GitHub-lagringsplatsen för en kopia av de Vignette-filer som ska köras i den här självstudien.

1. I RStudio navigerar du till fliken "Terminal" och CD: n till den katalog där du vill klona lagrings platsen.

1. Kör `git clone https://github.com/Azure/azureml-sdk-for-r` i terminalen för att klona lagrings platsen.

1. I RStudio navigerar du till mappen *vignettes* i mappen klonad *azureml-SDK-for-r* .  Under *vignettes* väljer du *först modellen träna-och-Deploy-First. RMD* -fil för att hitta Vignette som används i den här självstudien. De ytterligare filer som används för Vignette finns i undermappen *träna-och-distribuera-First-Model* . När du har öppnat Vignette anger du arbets katalogen till filens plats via **Session > anger arbets katalog > till käll filens plats**. 

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i "  *träna-and-Deploy-First-Model". RMD* -fil. Om du har erfarenhet av RMarkdown kan du använda koden från filen.  Eller så kan du kopiera/klistra in kodfragment från där, eller från den här artikeln till ett R-skript eller kommando raden. 


## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö
Installations programmet för ditt utvecklings arbete i den här självstudien innehåller följande åtgärder:

* Installera de paket som krävs
* Anslut till en arbets yta så att Compute-instansen kan kommunicera med fjär resurser
* Skapa ett experiment för att spåra dina körningar
* Skapa ett fjärrberäknings mål som ska användas för utbildning

### <a name="install-required-packages"></a>Installera de paket som krävs
Compute-instansen har redan den senaste versionen av R SDK från CRAN installerad. Om du vill installera utvecklings versionen från GitHub i stället för att hämta de senaste fel korrigeringarna kör du följande:
    
```R
remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
azuremlsdk::install_azureml()
```

> [!WARNING]
> Om du får frågan " `Would you like to install Miniconda? [Y/n]:` ", svarar du på "" `n` som beräknings instans redan har Anaconda installerat och en Miniconda-installation behövs under installations processen.

Nu ska du gå vidare och importera **azuremlsdk** -paketet.

```R
library(azuremlsdk)
```

Utbildnings-och Poäng skripten ( `accidents.R` och `accident_predict.R` ) har vissa ytterligare beroenden. Om du planerar att köra dessa skript lokalt ser du till att du har de nödvändiga paketen också.

### <a name="load-your-workspace"></a>Läs in din arbets yta
Skapa en instans av ett arbets områdes objekt från din befintliga arbets yta. Följande kod läser in arbets ytans detaljer från **config.jsi** filen. Du kan också hämta en arbets yta med [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Skapa ett experiment
Ett Azure ML-experiment spårar en gruppering av körningar, vanligt vis från samma utbildnings skript. Skapa ett experiment för att spåra körningarna för att träna cirkumflexs-modellen på olyckor-data.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Skapa ett beräknings mål
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
I den här självstudien används data från den amerikanska [nationella motorväg trafiksäker administration](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (tack vare [Mary C. Meyer och Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Den här data uppsättningen innehåller data från över 25 000 bil krascher i USA, med variabler som du kan använda för att förutsäga sannolikheten för en allvarlighet. Importera först data till R och omvandla dem till en ny dataframe `accidents` för analys, och exportera dem till en `Rdata` fil.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Ladda upp data till data lagret
Ladda upp data till molnet så att de kan komma åt den i din miljö för fjärr utbildning. Varje Azure Machine Learning arbets yta levereras med ett standard data lager som lagrar anslutnings informationen till den Azure Blob-behållare som är etablerad i det lagrings konto som är kopplat till arbets ytan. Följande kod överför de olycks data som du skapade ovan till det data lagret.

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
Ett utbildnings skript kallat `accidents.R` har angetts för dig i katalogen *träna-och-distribuera-First-Model* . Observera följande information **i övnings skriptet** som har utförts för att utnyttja Azure Machine Learning för utbildning:

* Övnings skriptet använder ett argument `-d` för att hitta den katalog som innehåller tränings data. När du definierar och skickar jobbet senare pekar du på data lagret för det här argumentet. Azure ML kommer att montera lagringsmappen till fjärrklusteret för utbildnings jobbet.
* Övnings skriptet loggar den slutliga noggrannheten som ett Mät värde för körnings posten i Azure ML med `log_metric_to_run()` . Azure ML SDK innehåller en uppsättning loggnings-API: er för att logga olika mått under inlärnings körningar. Dessa mått registreras och behålls i experiment körnings posten. Måtten kan sedan nås när som helst eller visas på sidan körnings information i [Studio](https://ml.azure.com). Se [referensen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) för en fullständig uppsättning loggnings metoder `log_*()` .
* Övnings skriptet sparar din modell i en katalog med namnet **outputs**. `./outputs`Mappen får särskild behandling av Azure ml. Under utbildningen överförs filer som skrivs till `./outputs` automatiskt till din körnings post av Azure ml och behålls som artefakter. Genom att spara den tränade modellen till `./outputs` , kommer du att kunna komma åt och hämta modell filen även när körningen är över och du inte längre har åtkomst till din fjärran sluten miljö.

### <a name="create-an-estimator"></a>Skapa ett beräkningsobjekt

En Azure ML-uppskattning kapslar in den körnings konfigurations information som krävs för att köra ett utbildnings skript på beräknings målet. Azure ML-körningar körs som behållare jobb på det angivna beräknings målet. Som standard innehåller Docker-avbildningen som skapats för utbildnings jobbet R, Azure ML SDK och en uppsättning ofta använda R-paket. Se den fullständiga listan med standard paket som ingår här.

Skapa en uppskattning genom att definiera:

* Den katalog som innehåller dina skript som krävs för utbildning ( `source_directory` ). Alla filer i den här katalogen överförs till klusternoderna för körning. Katalogen måste innehålla ditt utbildnings skript och eventuella ytterligare skript som krävs.
* Det utbildnings skript som ska köras ( `entry_script` ).
* Compute Target ( `compute_target` ), i det här fallet det AmlCompute-kluster som du skapade tidigare.
* De parametrar som krävs från övnings skriptet ( `script_params` ). Azure ML kommer att köra ditt utbildnings skript som ett kommando rads skript med `Rscript` . I den här självstudien anger du ett argument till skriptet, data katalogens monterings punkt, som du kan komma åt med `ds$path(target_path)` .
* Alla miljö beroenden som krävs för träning. Standard Docker-avbildningen som skapats för utbildning innehåller redan de tre paketen ( `caret` , `e1071` och `optparse` ) som krävs i övnings skriptet.  Så du behöver inte ange ytterligare information. Om du använder R-paket som inte ingår som standard använder du uppskattnings `cran_packages` parameterns parameter för att lägga till ytterligare cran-paket. Se [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) referensen för en fullständig uppsättning konfigurerbara alternativ.

```R
est <- estimator(source_directory = "train-and-deploy-first-model",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Skicka jobbet till det fjärranslutna klustret

Skicka slutligen jobbet som ska köras i klustret. `submit_experiment()` Returnerar ett körnings objekt som du sedan använder för att gränssnittet med körningen. Den första körningen tar totalt **cirka 10 minuter**. Men för senare körningar används samma Docker-avbildning igen så länge skript beroendena inte ändras.  I det här fallet cachelagras avbildningen och behållar start tiden är mycket snabbare.

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
I övnings skriptet `accidents.R` loggade du in ett mått från din modell: precisionen för förutsägelserna i tränings data. Du kan se mått i [Studio](https://ml.azure.com)eller extrahera dem till den lokala sessionen som en R-lista enligt följande:

```R
metrics <- get_run_metrics(run)
metrics
```

Om du har kört flera experiment (t. ex. genom att använda olika variabler, algoritmer eller standardvärden) kan du använda måtten från varje körning för att jämföra och välja den modell som du ska använda i produktionen.

### <a name="get-the-trained-model"></a>Hämta den tränade modellen
Du kan hämta den tränade modellen och titta på resultatet i din lokala R-session. Följande kod hämtar innehållet i `./outputs` katalogen, som innehåller modell filen.

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

Med din modell kan du förutsäga risken för dödsfall från en kollision. Använd Azure ML för att distribuera din modell som en förutsägelse tjänst. I den här självstudien ska du distribuera-webb tjänsten i [Azure Container instances](../container-instances/index.yml) (ACI).

### <a name="register-the-model"></a>Registrera modellen

Registrera först den modell som du laddade ned till din arbets yta med [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) . En registrerad modell kan vara en samling filer, men i det här fallet är R Model-objektet tillräckligt. Azure ML kommer att använda den registrerade modellen för distribution.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definiera beroenden för härledning
Om du vill skapa en webb tjänst för din modell måste du först skapa ett bedömnings skript ( `entry_script` ), ett R-skript som tar som variabler för indata (i JSON-format) och ger en förutsägelse från din modell. I den här självstudien använder du den angivna bedömnings filen `accident_predict.R` . Bedömnings skriptet måste innehålla en `init()` metod som läser in din modell och returnerar en funktion som använder modellen för att göra en förutsägelse baserad på indata. Mer information finns i [dokumentationen](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) .

Definiera sedan en Azure ML- **miljö** för ditt skripts paket beroenden. Med en miljö anger du R-paket (från CRAN eller någon annan stans) som behövs för att skriptet ska köras. Du kan också ange värden för miljövariabler som skriptet kan referera till för att ändra dess beteende. Som standard kommer Azure ML att bygga samma standard Docker-avbildning som används med uppskattningen för utbildning. Eftersom självstudien inte har några särskilda krav skapar du en miljö utan särskilda attribut.

```R
r_env <- r_environment(name = "basic_env")
```

Ange parametern om du vill använda en egen Docker-avbildning för distribution i stället `custom_docker_image` . Se [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) referensen för en fullständig uppsättning konfigurerbara alternativ för att definiera en miljö.

Nu har du allt du behöver för att skapa en **konfigurations härledning** för att kapsla in ditt bedömnings skript och miljö beroenden.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  source_directory = "train-and-deploy-first-model",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Distribuera till ACI
I den här självstudien ska du distribuera tjänsten till ACI. Den här koden tillhandahåller en enda behållare som svarar på inkommande begär Anden, vilket är lämpligt för testning och lätta inläsningar. Se [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) för ytterligare konfigurerbara alternativ. (För distributioner av produktions skala kan du även [distribuera till Azure Kubernetes-tjänsten](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html).)

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

Nu när din modell distribueras som en tjänst kan du testa tjänsten från R med [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) .  Ange en ny data uppsättning att förutse från, konvertera den till JSON och skicka den till tjänsten.

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

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* Nu när du har slutfört ditt första Azure Machine Learning experiment i R kan du läsa mer om [Azure Machine Learning SDK för r](https://azure.github.io/azureml-sdk-for-r/index.html).

* Läs mer om Azure Machine Learning med R från exemplen i de andra *vignettes* -mapparna.