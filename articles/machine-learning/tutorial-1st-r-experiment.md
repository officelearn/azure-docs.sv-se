---
title: 'Självstudiekurs: Använd R för att skapa en maskininlärningsmodell'
titleSuffix: Azure Machine Learning
description: I den här självstudien använder du Azure Machine Learning R SDK för att skapa en logistisk regressionsmodell som förutsäger sannolikheten för en dödsolycka i en bilolycka.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 72488ba339399c526e882ffd11c41410a0b011ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159106"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Självstudiekurs: Använd R för att skapa en maskininlärningsmodell
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien använder du Azure Machine Learning R SDK för att skapa en logistisk regressionsmodell som förutsäger sannolikheten för en dödsolycka i en bilolycka. Du ser hur Azure Machine Learning-molnresurserna fungerar med R för att tillhandahålla en skalbar miljö för utbildning och distribution av en modell.  

I den här självstudien utför du följande åtgärder:
> [!div class="checklist"]
> * Skapa en Azure Machine Learning-arbetsyta
> * Klona en anteckningsboksmapp med de filer som krävs för att köra den här självstudien i arbetsytan i din arbetsyta
> * Öppna RStudio från arbetsytan
> * Ladda data och förbered för träning
> * Ladda upp data till ett datalager så att den är tillgänglig för fjärrträning
> * Skapa en beräkningsresurs för att fjärrutreda modellen
> * Träna `caret` en modell för att förutsäga sannolikheten för dödsfall
> * Distribuera en slutpunkt för förutsägelse
> * Testa modellen från R

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.


## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning-arbetsyta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskininlärningsmodeller. Det binder din Azure-prenumeration och resursgrupp till ett lätt förbrukat objekt i tjänsten. 

Du skapar en arbetsyta via Azure-portalen, en webbaserad konsol för hantering av dina Azure-resurser. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Ta del av din **arbetsyta** och **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Klona en anteckningsboksmapp

I det här exemplet används molnservern för bärbara datorer på arbetsytan för en installationsfri och förkonfigurerad upplevelse. Använd [din egen miljö](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) om du föredrar att ha kontroll över din miljö, dina paket och beroenden.

Du slutför följande experimentinstallations- och körningssteg i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller verktygsinlärningsverktyg för att utföra datavetenskapsscenarier för datavetenskapsutövare på alla färdighetsnivåer.

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com/).

1. Välj din prenumeration och arbetsytan som du skapade.

1. Välj **Anteckningsböcker** till vänster.

1. Öppna mappen **Exempel.**

1. Öppna **R-mappen.**

1. Öppna mappen med ett versionsnummer på den.  Det här talet representerar den aktuella versionen för R SDK.

1. Välj **"..."** till höger om **vinjetter** mappen och välj sedan **Klon .**

    ![Klona mapp](media/tutorial-1st-r-experiment/clone-folder.png)

1. En lista med mappar visar varje användare som kommer åt arbetsytan.  Välj din mapp för att klona **vinjetter** mappen där.

## <a name="a-nameopenopen-rstudio"></a><a name="open">Öppna RStudio

Använd RStudio på en beräkningsinstans eller virtuell dator för bärbara datorer för att köra den här självstudien.  

1. Välj **Beräkna** till vänster.

1. Lägg till en beräkningsresurs om det inte redan finns någon beräkningsresurs.

1. När beräkningen körs använder du länken **RStudio** för att öppna RStudio.

1. I RStudio är *vinjettmappen* några nivåer ned från *användare* i avsnittet **Filer** längst ned till höger.  Under *vinjetter*väljer du mappen *train-and-deploy-to-aci* för att hitta de filer som behövs i den här självstudien.

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i *tåget och distribuera-till-aci. Rmd-fil.* Om du har erfarenhet av RMarkdown får du gärna använda koden från den filen.  Du kan också kopiera/klistra in kodavsnitten därifrån eller från den här artikeln till ett R-skript eller kommandoraden.  


## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö
Inställningarna för ditt utvecklingsarbete i den här självstudien innehåller följande åtgärder:

* Installera de paket som krävs
* Anslut till en arbetsyta så att beräkningsinstansen kan kommunicera med fjärrresurser
* Skapa ett experiment för att spåra dina körningar
* Skapa ett fjärrberäkningsmål som ska användas för utbildning

### <a name="install-required-packages"></a>Installera de paket som krävs
Den här självstudien förutsätter att du redan har Azure ML SDK installerat. Importera **azuremlsdk-paketet.**

```R
library(azuremlsdk)
```

Utbildning och poängsättning`accidents.R` skript `accident_predict.R`( och ) har några ytterligare beroenden. Om du planerar att köra dessa skript lokalt, se till att du har de nödvändiga paketen också.

### <a name="load-your-workspace"></a>Ladda arbetsytan
Instansiera ett arbetsyteobjekt från din befintliga arbetsyta. Följande kod läser in arbetsyteinformationen från **filen config.json.** Du kan också hämta [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)en arbetsyta med .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Skapa ett experiment
Ett Azure ML-experiment spårar en gruppering av körningar, vanligtvis från samma utbildningsskript. Skapa ett experiment för att spåra körningar för utbildning av caret-modellen på olycksdata.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål
Genom att använda Azure Machine Learning Compute (AmlCompute), en hanterad tjänst så kan datavetare träna maskininlärningsmodeller i kluster med virtuella Azure-datorer. Exempel innefattar virtuella datorer med GPU-stöd. I den här självstudien skapar du ett AmlCompute-kluster med en nod som träningsmiljö. Koden nedan skapar beräkningsklustret åt dig om det inte redan finns på arbetsytan.

Du kan behöva vänta några minuter på att beräkningsklustret ska etableras om det inte redan finns.

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
Denna handledning använder data från US [National Highway Traffic Safety Administration](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (med tack vare Mary [C. Meyer och Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Den här datauppsättningen innehåller data från över 25 000 bilolyckor i USA, med variabler som du kan använda för att förutsäga sannolikheten för dödsfall. Importera först data till R och omvandla dem `accidents` till en ny dataram för analys och exportera den till en `Rdata` fil.

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

### <a name="upload-data-to-the-datastore"></a>Ladda upp data till databutiken
Ladda upp data till molnet så att de kan komma åt av din fjärrträningsmiljö. Varje Azure Machine Learning-arbetsyta levereras med ett standarddatalager som lagrar anslutningsinformationen till Azure blob-behållaren som är etablerad i lagringskontot som är kopplat till arbetsytan. Följande kod kommer att ladda upp olycksdata som du skapade ovan till det datalagret.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Träna en modell

Den här självstudien får du plats med en logistisk regressionsmodell på dina uppladdade data med hjälp av fjärrberäkningsklustret. Om du vill skicka in ett jobb måste du:

* Förbered utbildningsskriptet
* Skapa ett beräkningsobjekt
* Skicka jobbet

### <a name="prepare-the-training-script"></a>Förbered utbildningsskriptet
Ett utbildningsskript `accidents.R` som anropas har tillhandahållits för dig i samma katalog som den här självstudien. Lägg märke till följande information **i utbildningsskriptet** som har gjorts för att utnyttja Azure Machine Learning för utbildning:

* Utbildningsskriptet tar `-d` ett argument för att hitta katalogen som innehåller träningsdata. När du definierar och skickar jobbet senare pekar du på datalagret för det här argumentet. Azure ML monterar lagringsmappen till fjärrklustret för utbildningsjobbet.
* Utbildningsskriptet loggar den slutliga noggrannheten som ett mått till `log_metric_to_run()`körningsposten i Azure ML med . Azure ML SDK innehåller en uppsättning loggnings-API:er för loggning av olika mått under utbildningskörningar. Dessa mått registreras och sparas i experimentkörningsposten. Måtten kan sedan nås när som helst eller visas på sidan körinformation i [studion](https://ml.azure.com). Se [referensen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) för hela uppsättningen `log_*()`loggningsmetoder .
* Utbildningsskriptet sparar din modell i en katalog med namnet **outputs**. Mappen `./outputs` får särskild behandling av Azure ML. Under träningen överförs `./outputs` filer som skrivs till automatiskt till din körningspost av Azure ML och sparas som artefakter. Genom att spara `./outputs`den tränade modellen till kan du komma åt och hämta din modellfil även efter körningen är över och du inte längre har tillgång till din fjärrträningsmiljö.

### <a name="create-an-estimator"></a>Skapa ett beräkningsobjekt

En Azure ML-uppskattningsanordning kapslar in den körningskonfigurationsinformation som behövs för att köra ett utbildningsskript på beräkningsmålet. Azure ML-körningar körs som behållarjobb på det angivna beräkningsmålet. Som standard kommer Docker-avbildningen som skapats för ditt utbildningsarbete att innehålla R, Azure ML SDK och en uppsättning vanliga R-paket. Se hela listan över standardpaket som ingår här.

Om du vill skapa kalkylatorn definierar du:

* Katalogen som innehåller skripten som`source_directory`behövs för utbildning ( ). Alla filer i den här katalogen överförs till klusternoderna för körning. Katalogen måste innehålla ditt utbildningsskript och eventuella ytterligare skript som krävs.
* Utbildningsskriptet som ska köras (`entry_script`).
* Beräkningsmålet`compute_target`( ), i det här fallet AmlCompute-klustret som du skapade tidigare.
* De parametrar som krävs`script_params`från utbildningsskriptet ( ). Azure ML kör ditt träningsskript som ett `Rscript`kommandoradsskript med . I den här självstudien anger du ett argument till skriptet, datakatalogens monteringspunkt, som du kan komma åt med `ds$path(target_path)`.
* Alla miljöberoenden som krävs för utbildning. Den standardavbildning av Docker som skapats`caret`för `e1071`utbildning `optparse`innehåller redan de tre paket ( , och ) som behövs i utbildningsskriptet.  Så du behöver inte ange ytterligare information. Om du använder R-paket som inte ingår som standard använder `cran_packages` du uppskattningsparametern för att lägga till ytterligare CRAN-paket. Se [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) referensen för hela uppsättningen konfigurerbara alternativ.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Skicka jobbet i fjärrklustret

Skicka slutligen jobbet för att köras i klustret. `submit_experiment()`returnerar ett Run-objekt som du sedan använder för att samverka med körningen. Den första körningen tar totalt **cirka 10 minuter**. Men för senare körningar återanvänds samma Docker-avbildning så länge skriptberoendena inte ändras.  I det här fallet cachelagras avbildningen och behållarens starttid är mycket snabbare.

```R
run <- submit_experiment(exp, est)
```

Du kan visa körningens information i RStudio Viewer. Om du klickar på länken "Webbvy" kommer du till Azure Machine Learning studio, där du kan övervaka körningen i användargränssnittet.

```R
view_run_details(run)
```

Modellutbildning sker i bakgrunden. Vänta tills modellen har slutfört träningen innan du kör mer kod.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Du – och kollegor med åtkomst till arbetsytan – kan skicka flera experiment parallellt och Azure ML tar för att schemalägga uppgifterna i beräkningsklustret. Du kan även konfigurera klustret för att automatiskt skala upp till flera noder och skala tillbaka när det inte finns några fler beräkningsuppgifter i kön. Den här konfigurationen är ett kostnadseffektivt sätt för team att dela beräkningsresurser.

## <a name="retrieve-training-results"></a>Hämta träningsresultat
När din modell har slutfört träningen kan du komma åt artefakterna för ditt jobb som sparades i körningsposten, inklusive alla mått som loggats och den slutliga tränade modellen.

### <a name="get-the-logged-metrics"></a>Hämta de loggade måtten
I utbildningsskriptet `accidents.R`loggade du ett mått från din modell: precisionen i förutsägelserna i träningsdata. Du kan se mått i [studion](https://ml.azure.com)eller extrahera dem till den lokala sessionen som en R-lista enligt följande:

```R
metrics <- get_run_metrics(run)
metrics
```

Om du har kört flera experiment (t.o.d. med hjälp av olika variabler, algoritmer eller hyperparamerer) kan du använda måtten från varje körning för att jämföra och välja den modell som du ska använda i produktionen.

### <a name="get-the-trained-model"></a>Skaffa den tränade modellen
Du kan hämta den tränade modellen och titta på resultaten i din lokala R-session. Följande kod hämtar innehållet i `./outputs` katalogen, som innehåller modellfilen.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Du ser några faktorer som bidrar till en ökning av den uppskattade sannolikheten för dödsfall:

* högre slaghastighet 
* manlig förare
* äldre boende
* Passagerare

Du ser lägre sannolikheter för döden med:

* förekomst av krockkuddar
* närvaro säkerhetsbälten
* frontalkollision 

Fordonets tillverkningsår har ingen signifikant effekt.

Du kan använda den här modellen för att göra nya förutsägelser:

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

Med din modell kan du förutsäga risken för dödsfall från en kollision. Använd Azure ML för att distribuera din modell som en förutsägelsetjänst. I den här självstudien distribuerar du webbtjänsten i [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Registrera modellen

Registrera först den modell som du hämtade [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)till arbetsytan med . En registrerad modell kan vara valfri samling av filer, men i det här fallet R-modellobjektet är tillräckligt. Azure ML använder den registrerade modellen för distribution.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definiera inferensberoenden
Om du vill skapa en webbtjänst för modellen måste`entry_script`du först skapa ett bedömningsskript ( ), ett R-skript som tar som indatavariabelvärden (i JSON-format) och skickar ut en förutsägelse från din modell. Använd den medföljande poängfilen `accident_predict.R`för den här självstudien . Bedömningsskriptet måste `init()` innehålla en metod som läser in din modell och returnerar en funktion som använder modellen för att göra en förutsägelse baserat på indata. Mer information finns i [dokumentationen.](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details)

Definiera sedan en Azure **ML-miljö** för skriptets paketberoenden. Med en miljö anger du R-paket (från CRAN eller någon annanstans) som behövs för att skriptet ska kunna köras. Du kan också ange värden för miljövariabler som skriptet kan referera till för att ändra dess beteende. Som standard kommer Azure ML att skapa samma standarddockeravbildning som används med uppskattningen för utbildning. Eftersom självstudien inte har några särskilda krav skapar du en miljö utan särskilda attribut.

```R
r_env <- r_environment(name = "basic_env")
```

Om du vill använda din egen Docker-avbildning för distribution i stället anger du parametern. `custom_docker_image` Se [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) referensen för den fullständiga uppsättningen konfigurerbara alternativ för att definiera en miljö.

Nu har du allt du behöver för att skapa en **inferens config** för kapsla in din scoring script och miljö beroenden.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Distribuera till ACI
I den här självstudien distribuerar du tjänsten till ACI. Denna kod etablerar en enda behållare för att svara på inkommande förfrågningar, som är lämplig för provning och lätta belastningar. Se [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) för ytterligare konfigurerbara alternativ. (För distributioner av produktionsskala kan du också [distribuera till Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Nu distribuerar du din modell som en webbtjänst. Distributionen **kan ta flera minuter**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testa den distribuerade tjänsten

Nu när din modell har distribuerats som en [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)tjänst kan du testa tjänsten från R med .  Ange en ny uppsättning data som ska förutsägas från, konvertera den till JSON och skicka den till tjänsten.

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

Du kan också hämta webbtjänstens HTTP-slutpunkt, som accepterar REST-klientanrop. Du kan dela den här slutpunkten med alla som vill testa webbtjänsten eller integrera den i ett program.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurserna när du inte längre behöver dem. Ta inte bort någon resurs som du planerar att fortfarande använda. 

Ta bort webbtjänsten:
```R
delete_webservice(aci_service)
```

Ta bort den registrerade modellen:
```R
delete_model(model)
```

Ta bort beräkningsklustret:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Ta bort allt

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* Nu när du har slutfört ditt första Azure Machine Learning-experiment i R kan du läsa mer om [Azure Machine Learning SDK för R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Läs mer om Azure Machine Learning med R från exemplen i de andra *vinjetter mapparna.*
