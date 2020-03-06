---
title: Träna och distribuera modeller från CLI
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Machine Learning-tillägget för Azure CLI för att träna, registrera och distribuera en modell från kommando raden.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 70fa17e3e6f91bf393865cc979a8e47e4bf8687b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393341"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Självstudie: träna och distribuera en modell från CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien använder du Machine Learning-tillägget för Azure CLI för att träna, registrera och distribuera en modell.

I den här självstudien för python [-utbildning används scikit – lär dig](https://scikit-learn.org/) att träna en grundläggande modell. Den här självstudien fokuserar inte på skripten eller modellen, men processen för att använda CLI för att arbeta med Azure Machine Learning.

Läs hur du vidtar följande åtgärder:

> [!div class="checklist"]
> * Installera Machine Learning-tillägget
> * Skapa en Azure Machine Learning-arbetsyta
> * Skapa den beräknings resurs som används för att träna modellen
> * Definiera och registrera den data uppsättning som används för att träna modellen
> * Starta en tränings körning
> * Registrera och ladda ned en modell
> * Distribuera modellen som en webb tjänst
> * Poäng data med hjälp av webb tjänsten

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnads fritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)kan CLI nås via webbläsaren och finns i molnet.

## <a name="download-the-example-project"></a>Ladda ned exempel projektet

I den här självstudien hämtar du [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) -projektet. Filerna i `examples/cli-train-deploy`-katalogen används av stegen i den här självstudien.

Hämta en lokal kopia av filerna genom att antingen [Hämta ett. zip-arkiv](https://github.com/microsoft/MLOps/archive/master.zip)eller använda följande git-kommando för att klona lagrings platsen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Träna filer

`examples/cli-train-deploy` katalog från projektet innehåller följande filer, som används när du tränar en modell:

* `.azureml\mnist.runconfig`: en __körnings konfigurations__ fil. Den här filen definierar den körnings miljö som behövs för att träna modellen. I det här exemplet monteras även de data som används för att träna modellen i tränings miljön.
* `scripts\train.py`: utbildnings skriptet. Den här filen tränar modellen.
* `scripts\utils.py`: en hjälp fil som används av övnings skriptet.
* `.azureml\conda_dependencies.yml`: definierar de program beroenden som krävs för att köra utbildnings skriptet.
* `dataset.json`: data uppsättnings definitionen. Används för att registrera MNIST-datauppsättningen på arbets ytan Azure Machine Learning.

### <a name="deployment-files"></a>Distributions filer

Databasen innehåller följande filer, som används för att distribuera den utbildade modellen som en webb tjänst:

* `aciDeploymentConfig.yml`: en __distributions konfigurations__ fil. Den här filen definierar den värd miljö som behövs för modellen.
* `inferenceConfig.yml`: en __konfigurations__ fil för härledning. Den här filen definierar den program miljö som används av tjänsten för att räkna data med modellen.
* `score.py`: ett Python-skript som accepterar inkommande data, visar det med modellen och returnerar sedan ett svar.
* `scoring-env.yml`: de Conda-beroenden som behövs för att köra modellen och `score.py` skriptet.
* `testdata.json`: en datafil som kan användas för att testa den distribuerade webb tjänsten.

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Den mest grundläggande är att interaktivt autentisera med hjälp av en webbläsare. För att autentisera interaktivt, öppna en kommando rad eller Terminal och Använd följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommando raden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

## <a name="install-the-machine-learning-extension"></a>Installera Machine Learning-tillägget

Om du vill installera Machine Learning-tillägget använder du följande kommando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Om du får ett meddelande om att tillägget redan är installerat använder du följande kommando för att uppdatera till den senaste versionen:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resurs grupp är en grundläggande behållare för resurser på Azure-plattformen. När du arbetar med Azure Machine Learning kommer resurs gruppen att innehålla arbets ytan Azure Machine Learning. Den kommer också att innehålla andra Azure-tjänster som används av arbets ytan. Om du till exempel tränar din modell med en molnbaserad beräknings resurs skapas den resursen i resurs gruppen.

Använd följande kommando för att __skapa en ny resurs grupp__. Ersätt `<resource-group-name>` med namnet som ska användas för den här resurs gruppen. Ersätt `<location>` med den Azure-region som ska användas för den här resurs gruppen:

> [!TIP]
> Välj en region där Azure Machine Learning är tillgänglig. Mer information finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Svaret från det här kommandot liknar följande JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Mer information om hur du arbetar med resurs grupper finns i [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Använd följande kommando för att skapa en ny arbets yta. Ersätt `<workspace-name>` med det namn som du vill använda för den här arbets ytan. Ersätt `<resource-group-name>` med namnet på resurs gruppen:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Anslut det lokala projektet till arbets ytan

Från en terminal-eller kommando tolk använder du följande kommandon för att ändra kataloger till `cli-train-deploy` katalogen och ansluter sedan till din arbets yta:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Det här kommandot skapar en `.azureml/config.json`-fil som innehåller information som behövs för att ansluta till din arbets yta. Resten av de `az ml`-kommandon som används i den här självstudien kommer att använda den här filen, så du behöver inte lägga till arbets ytan och resurs gruppen i alla kommandon.

## <a name="create-the-compute-target-for-training"></a>Skapa Compute-målet för utbildning

I det här exemplet används ett Azure Machine Learning beräknings kluster för att träna modellen. Använd följande kommando för att skapa ett nytt beräknings kluster:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Det här kommandot skapar ett nytt Compute-mål med namnet `cpu`, med högst fyra noder. Den VM-storlek som valts tillhandahåller en virtuell dator med en GPU-resurs. Information om storleken på den virtuella datorn finns i [VM-typer och storlekar].

> [!IMPORTANT]
> Namnet på beräknings målet (`cpu` i det här fallet) är viktigt. den `.azureml/mnist.runconfig`-fil som används i nästa avsnitt refereras till.

## <a name="define-the-dataset"></a>Definiera data uppsättningen

För att träna en modell kan du ange utbildnings data med hjälp av en data uppsättning. Om du vill skapa en data uppsättning från CLI måste du ange en definitions fil för data uppsättning. `dataset.json`-filen som anges i lagrings platsen skapar en ny data uppsättning med hjälp av MNIST-data. Den data uppsättning som skapas heter `mnist-dataset`.

Om du vill registrera data uppsättningen med hjälp av `dataset.json`-filen använder du följande kommando:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```


> [!IMPORTANT]
> Kopiera värdet för `id` posten, som det används i nästa avsnitt.

Om du vill se en mer omfattande mall för en data uppsättning använder du följande kommando:
```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Referera till data uppsättningen

Om du vill göra data uppsättningen tillgänglig i tränings miljön måste du referera till den från runconfig-filen. `.azureml/mnist.runconfig`-filen innehåller följande YAML-poster:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Ändra värdet för `id` posten så att den matchar värdet som returnerades när du registrerade data uppsättningen. Det här värdet används för att läsa in data till beräknings målet under träningen.

Den här YAML resulterar i följande åtgärder under utbildningen:

* Monterar data uppsättningen (baserat på data uppsättningens ID) i tränings miljön och lagrar sökvägen till monterings punkten i `mnist`-miljövariabeln.
* Överför platsen för data (monterings punkten) i övnings miljön till skriptet med hjälp av argumentet `--data-folder`.

Runconfig-filen innehåller också information som används för att konfigurera den miljö som används av tränings körningen. Om du inspekterar den här filen ser du att den refererar till `cpu-compute` Compute Target som du skapade tidigare. Det visar också antalet noder som ska användas när utbildning (`"nodeCount": "4"`), och innehåller ett `"condaDependencies"`-avsnitt som visar de python-paket som krävs för att köra övnings skriptet.

> [!TIP]
> Även om det är möjligt att skapa en runconfig-fil manuellt, skapades den i det här exemplet med den `generate-runconfig.py`-fil som finns i lagrings platsen. Den här filen hämtar en referens till den registrerade data uppsättningen, skapar en körnings konfigurations program mässigt och sparar den sedan i filen.

Mer information om att köra konfigurationsfiler finns i [Konfigurera och använda Compute-mål för modell träning](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). En fullständig JSON-referens finns i [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Skicka in utbildnings körningen

Om du vill starta en utbildning i `cpu-compute` Compute Target använder du följande kommando:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Det här kommandot anger ett namn på experimentet (`myexperiment`). Experimentet lagrar information om den här körningen på arbets ytan.

Parametern `-c mnist` anger `.azureml/mnist.runconfig`s filen.

Parametern `-t` lagrar en referens till den här körningen i en JSON-fil och kommer att användas i nästa steg för att registrera och ladda ned modellen.

När inlärnings körnings processen strömmar den information från utbildningen på fjärrdatorns beräknings resurs. En del av informationen liknar följande text:

```text
Predict the test set
Accuracy is 0.9185
```

Den här texten loggas från övnings skriptet och visar modellens precision. Andra modeller kommer att ha olika prestanda mått.

Om du inspekterar övnings skriptet ser du att det också använder alfa värdet när den tränade modellen lagras till `outputs/sklearn_mnist_model.pkl`.

Modellen sparades i `./outputs`-katalogen på beräknings målet där den tränades. I det här fallet är Azure Machine Learning beräknings instansen i Azure-molnet. Inlärnings processen överför automatiskt innehållet i `./outputs`-katalogen från beräknings målet där inlärningen sker till Azure Machine Learning-arbetsytan. Den lagras som en del av experimentet (`myexperiment` i det här exemplet).

## <a name="register-the-model"></a>Registrera modellen

Om du vill registrera modellen direkt från den lagrade versionen i experimentet använder du följande kommando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Det här kommandot registrerar `outputs/sklearn_mnist_model.pkl`-filen som skapats av träningen och körs som en ny modell registrering med namnet `mymodel`. `--assets-path` refererar till en sökväg i ett experiment. I det här fallet läses experimentet och körnings informationen in från `runoutput.json`-filen som skapats av övnings kommandot. `-t registeredmodel.json` skapar en JSON-fil som refererar till den nya registrerade modellen som skapats med det här kommandot och används av andra CLI-kommandon som fungerar med registrerade modeller.

Utdata från det här kommandot liknar följande JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Modell version

Notera det versions nummer som returnerades för modellen. Versionen ökas varje gången du registrerar en ny modell med det här namnet. Du kan till exempel Ladda ned modellen och registrera den från en lokal fil med hjälp av följande kommandon:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Det första kommandot laddar ned den registrerade modellen till den aktuella katalogen. Fil namnet är `sklearn_mnist_model.pkl`, vilket är den fil som refereras när du registrerade modellen. Det andra kommandot registrerar den lokala modellen (`-p "sklearn_mnist_model.pkl"`) med samma namn som den tidigare registreringen (`mymodel`). Den här gången visar de JSON-data som returnerade versionen som 2.

## <a name="deploy-the-model"></a>Distribuera modellen

Använd följande kommando för att distribuera en modell:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Du kan få en varning om att "Det gick inte att kontrol lera LocalWebservice-existens" eller "Det gick inte att skapa Docker-klienten". Du kan ignorera detta på ett säkert sätt eftersom du inte distribuerar en lokal webb tjänst.

Det här kommandot distribuerar en ny tjänst med namnet `myservice`, med version 1 av den modell som du registrerade tidigare.

`inferenceConfig.yml`-filen innehåller information om hur du använder modellen för härledning. Till exempel refererar den till Start-skriptet (`score.py`) och program beroenden. 

Mer information om strukturen för den här filen finns i schemat för [konfiguration av energischemat](reference-azure-machine-learning-cli.md#inference-configuration-schema). Mer information om Entry-skript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

`aciDeploymentConfig.yml` beskriver distributions miljön som används som värd för tjänsten. Distributions konfigurationen är speciell för den beräknings typ som du använder för distributionen. I det här fallet används en Azure Container instance. Mer information finns i [konfigurations schema för distribution](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Det tar flera minuter innan distributions processen har slutförts.

> [!TIP]
> I det här exemplet används Azure Container Instances. Distributioner till ACI skapar automatiskt den nödvändiga ACI-resursen. Om du i stället vill distribuera till Azure Kubernetes-tjänsten måste du skapa ett AKS-kluster i förväg och ange det som en del av kommandot `az ml model deploy`. Ett exempel på hur du distribuerar till AKS finns i [distribuera en modell till ett Azure Kubernetes service-kluster](how-to-deploy-azure-kubernetes-service.md).

Efter flera minuter returneras information som liknar följande JSON:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Bedömnings-URI

`scoringUri` som returnerades från distributionen är en REST-slutpunkt för en modell som distribueras som en webb tjänst. Du kan också få denna URI genom att använda följande kommando:

```azurecli-interactive
az ml service show -n myservice
```

Det här kommandot returnerar samma JSON-dokument, inklusive `scoringUri`.

REST-slutpunkten kan användas för att skicka data till tjänsten. Information om hur du skapar ett klient program som skickar data till tjänsten finns i [använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Skicka data till tjänsten

Även om du kan skapa ett klient program för att anropa slut punkten, innehåller Machine Learning CLI ett verktyg som kan fungera som en test klient. Använd följande kommando för att skicka data i `testdata.json`-filen till tjänsten:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Om du använder PowerShell använder du följande kommando i stället:
>
> ```powershell
> az ml service run -n myservice -d `@testdata.json
> ```

Svaret från kommandot liknar `[ 3 ]`.

## <a name="clean-up-resources"></a>Rensa resurser

> [!IMPORTANT]
> Resurser som har skapats kan användas som förutsättningar för att andra självstudier och instruktionsartiklar om Azure Machine Learning.

### <a name="delete-deployed-service"></a>Ta bort distribuerad tjänst

Om du planerar att fortsätta använda Azure Machine Learning arbets ytan, men vill ta bort den distribuerade tjänsten för att minska kostnaderna, använder du följande kommando:

```azurecli-interactive
az ml service delete -n myservice
```

Det här kommandot returnerar ett JSON-dokument som innehåller namnet på den borttagna tjänsten. Det kan ta flera minuter innan tjänsten tas bort.

### <a name="delete-the-training-compute"></a>Ta bort inlärnings beräkningen

Om du planerar att fortsätta använda Azure Machine Learning arbets ytan, men vill ta bort `cpu-compute` Compute-målet som skapats för utbildning, använder du följande kommando:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Det här kommandot returnerar ett JSON-dokument som innehåller ID: t för det borttagna beräknings målet. Det kan ta flera minuter innan Compute-målet har tagits bort.

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda de resurser som du har skapat tar du bort dem så att du inte debiteras ytterligare avgifter.

Om du vill ta bort resurs gruppen och alla Azure-resurser som skapats i det här dokumentet, använder du följande kommando. Ersätt `<resource-group-name>` med namnet på resurs gruppen du skapade tidigare:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Nästa steg

I den här Azure Machine Learning självstudien använde du Machine Learning CLI för följande uppgifter:

> [!div class="checklist"]
> * Installera Machine Learning-tillägget
> * Skapa en Azure Machine Learning-arbetsyta
> * Skapa den beräknings resurs som används för att träna modellen
> * Definiera och registrera den data uppsättning som används för att träna modellen
> * Starta en tränings körning
> * Registrera och ladda ned en modell
> * Distribuera modellen som en webb tjänst
> * Poäng data med hjälp av webb tjänsten

Mer information om hur du använder CLI finns i [Använd CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
