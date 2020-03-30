---
title: Träna och driftsätta modeller från CLI
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder maskininlärningstillägget för Azure CLI för att träna, registrera och distribuera en modell från kommandoraden.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 401ce2aed2c783169592f0dc664a3a7baea415b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336614"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Självstudiekurs: Träna och distribuera en modell från CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien använder du maskininlärningstillägget för Azure CLI för att träna, registrera och distribuera en modell.

Python-utbildningsskripten i den här självstudien använder [scikit-learn](https://scikit-learn.org/) för att träna en grundläggande modell. Fokus för den här självstudien är inte på skripten eller modellen, utan processen att använda CLI för att arbeta med Azure Machine Learning.

Läs hur du vidtar följande åtgärder:

> [!div class="checklist"]
> * Installera maskininlärningstillägget
> * Skapa en Azure Machine Learning-arbetsyta
> * Skapa beräkningsresursen som används för att träna modellen
> * Definiera och registrera den datauppsättning som används för att träna modellen
> * Starta en träningsrunda
> * Registrera och ladda ner en modell
> * Distribuera modellen som en webbtjänst
> * Poängdata med hjälp av webbtjänsten

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)nås CLI via webbläsaren och finns i molnet.

## <a name="download-the-example-project"></a>Ladda ner exempelprojektet

Ladda ned projektet [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) för den här självstudien. Filerna i `examples/cli-train-deploy` katalogen används av stegen i den här självstudien.

Om du vill hämta en lokal kopia av filerna hämtar du antingen [ett ZIP-arkiv](https://github.com/microsoft/MLOps/archive/master.zip)eller använder följande Git-kommando för att klona databasen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Utbildningsfiler

Katalogen `examples/cli-train-deploy` från projektet innehåller följande filer, som används när du tränar en modell:

* `.azureml\mnist.runconfig`: En __körningskonfigurationsfil.__ Den här filen definierar den körningsmiljö som behövs för att träna modellen. I det här exemplet monteras också de data som används för att träna modellen i träningsmiljön.
* `scripts\train.py`: Utbildningsmanuset. Den här filen tränar modellen.
* `scripts\utils.py`: En hjälpfil som används av utbildningsskriptet.
* `.azureml\conda_dependencies.yml`: Definierar de programvaruberoenden som behövs för att köra utbildningsskriptet.
* `dataset.json`: Datauppsättningsdefinitionen. Används för att registrera MNIST-datauppsättningen på arbetsytan Azure Machine Learning.

### <a name="deployment-files"></a>Distributionsfiler

Databasen innehåller följande filer som används för att distribuera den tränade modellen som en webbtjänst:

* `aciDeploymentConfig.yml`: En __distributionskonfigurationsfil.__ Den här filen definierar den värdmiljö som behövs för modellen.
* `inferenceConfig.json`: En __slutledning konfigurationsfil.__ Den här filen definierar den programvarumiljö som används av tjänsten för att poängsätta data med modellen.
* `score.py`: Ett pythonskript som accepterar inkommande data, gör poäng med hjälp av modellen och returnerar sedan ett svar.
* `scoring-env.yml`: Conda-beroenden som behövs `score.py` för att köra modellen och skriptet.
* `testdata.json`: En datafil som kan användas för att testa den distribuerade webbtjänsten.

## <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Det mest grundläggande är att interaktivt autentisera med hjälp av en webbläsare. Om du vill autentisera interaktivt öppnar du en kommandorad eller terminal och använder följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa instruktionerna på kommandoraden. Instruktionerna innebär att du [https://aka.ms/devicelogin](https://aka.ms/devicelogin) surfar på och anger en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

## <a name="install-the-machine-learning-extension"></a>Installera maskininlärningstillägget

Så här installerar du maskininlärningstillägget:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Om du får ett meddelande om att tillägget redan är installerat använder du följande kommando för att uppdatera till den senaste versionen:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en grundläggande behållare med resurser på Azure-plattformen. När du arbetar med Azure Machine Learning innehåller resursgruppen din Azure Machine Learning-arbetsyta. Den innehåller även andra Azure-tjänster som används av arbetsytan. Om du till exempel tränar din modell med hjälp av en molnbaserad beräkningsresurs skapas resursen i resursgruppen.

Om du vill __skapa en ny resursgrupp__använder du följande kommando. Ersätt `<resource-group-name>` med namnet som ska användas för den här resursgruppen. Ersätt `<location>` med Azure-regionen som ska användas för den här resursgruppen:

> [!TIP]
> Du bör välja en region där Azure Machine Learning är tillgängligt. Mer information finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

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

Mer information om hur du arbetar med resursgrupper finns i [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du vill skapa en ny arbetsyta använder du följande kommando. Ersätt `<workspace-name>` med det namn som du vill använda för den här arbetsytan. Ersätt `<resource-group-name>` med namnet på resursgruppen:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Utdata för det här kommandot liknar följande JSON:

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

## <a name="connect-local-project-to-workspace"></a>Ansluta lokalt projekt till arbetsytan

Från en terminal eller kommandotolk använder du följande `cli-train-deploy` kommandon som ändrar kataloger till katalogen och ansluter sedan till arbetsytan:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Utdata för det här kommandot liknar följande JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Det här kommandot `.azureml/config.json` skapar en fil som innehåller information som behövs för att ansluta till arbetsytan. Resten av `az ml` kommandona som används i den här självstudien använder den här filen, så du behöver inte lägga till arbetsytan och resursgruppen i alla kommandon.

## <a name="create-the-compute-target-for-training"></a>Skapa beräkningsmålet för träning

I det här exemplet används ett Azure Machine Learning Compute-kluster för att träna modellen. Om du vill skapa ett nytt beräkningskluster använder du följande kommando:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Utdata för det här kommandot liknar följande JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Det här kommandot skapar ett `cpu-cluster`nytt beräkningsmål med namnet , med högst fyra noder. Den valda VM-storleken ger en virtuell dator en GPU-resurs. Information om storleken på den virtuella datorn finns i [VM-typer och storlekar].

> [!IMPORTANT]
> Namnet på beräkningsmålet`cpu-cluster` ( i det här fallet), är viktigt; Den refereras av `.azureml/mnist.runconfig` filen som används i nästa avsnitt.

## <a name="define-the-dataset"></a>Definiera datauppsättningen

Om du vill träna en modell kan du tillhandahålla träningsdata med hjälp av en datauppsättning. Om du vill skapa en datauppsättning från CLI måste du ange en datauppsättningsdefinitionsfil. Filen `dataset.json` som finns i repo skapar en ny datauppsättning med hjälp av MNIST-data. Den datauppsättning som skapas `mnist-dataset`heter .

Om du vill registrera `dataset.json` datauppsättningen med hjälp av filen använder du följande kommando:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Utdata för det här kommandot liknar följande JSON:

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
> Kopiera värdet för `id` transaktionen, som det används i nästa avsnitt.

Om du vill se en mer omfattande mall för en datauppsättning använder du följande kommando:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Referera till datauppsättningen

Om du vill göra datauppsättningen tillgänglig i träningsmiljön måste du referera till den från runconfig-filen. Filen `.azureml/mnist.runconfig` innehåller följande YAML-poster:

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

Ändra värdet för `id` transaktionen så att det matchar det värde som returnerades när du registrerade datauppsättningen. Det här värdet används för att läsa in data i beräkningsmålet under träningen.

Detta YAML resulterar i följande åtgärder under utbildningen:

* Monterar datauppsättningen (baserat på datauppsättningens ID) i träningsmiljön och lagrar sökvägen `mnist` till monteringspunkten i miljövariabeln.
* Skickar platsen för data (monteringspunkt) i träningsmiljön `--data-folder` till skriptet med argumentet.

Runconfig-filen innehåller också information som används för att konfigurera miljön som används av träningskörningen. Om du granskar den här filen ser du `cpu-compute` att den refererar till beräkningsmålet som du skapade tidigare. Den visar också antalet noder som`"nodeCount": "4"`ska användas vid `"condaDependencies"` utbildning ( ), och innehåller ett avsnitt som listar Python-paket som behövs för att köra utbildningsskriptet.

> [!TIP]
> Det är möjligt att manuellt skapa en runconfig-fil, men `generate-runconfig.py` den i det här exemplet skapades med hjälp av filen som ingår i databasen. Den här filen hämtar en referens till den registrerade datauppsättningen, skapar en körning konfidiens programatiskt och beständig den sedan för att filen ska arkiveras.

Mer information om körningskonfigurationsfiler finns i [Konfigurera och använda beräkningsmål för modellutbildning](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). En komplett JSON-referens finns i [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Skicka in utbildningskörningen

Om du vill starta `cpu-cluster` en träningskörning på beräkningsmålet använder du följande kommando:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Det här kommandot anger ett`myexperiment`namn för experimentet ( ). Experimentet lagrar information om den här körningen på arbetsytan.

Parametern `-c mnist` anger `.azureml/mnist.runconfig` filen.

Parametern `-t` lagrar en referens till den här körningen i en JSON-fil och kommer att användas i nästa steg för att registrera och hämta modellen.

När utbildningskörningen bearbetas strömmar den information från träningspasset på fjärrberäkningsresursen. En del av informationen liknar följande text:

```output
Predict the test set
Accuracy is 0.9185
```

Den här texten loggas från träningsskriptet och visar modellens noggrannhet. Andra modeller kommer att ha olika prestandamått.

Om du inspekterar utbildningsskriptet kommer du att märka att det också `outputs/sklearn_mnist_model.pkl`använder alfavärdet när den lagrar den tränade modellen till .

Modellen sparades i `./outputs` katalogen på beräkningsmålet där den tränades. I det här fallet Azure Machine Learning Compute-instansen i Azure-molnet. Utbildningsprocessen överför automatiskt innehållet i `./outputs` katalogen från beräkningsmålet där utbildning sker på din Azure Machine Learning-arbetsyta. Den lagras som en del`myexperiment` av experimentet (i det här exemplet).

## <a name="register-the-model"></a>Registrera modellen

Om du vill registrera modellen direkt från den lagrade versionen i experimentet använder du följande kommando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Det här kommandot `outputs/sklearn_mnist_model.pkl` registrerar filen som skapats av `mymodel`utbildningskörningen som en ny modellregistrering med namnet . Referenserna `--assets-path` till en sökväg i ett experiment. I det här fallet läses experiment- `runoutput.json` och körningsinformationen in från filen som skapats av träningskommandot. Skapar `-t registeredmodel.json` en JSON-fil som refererar till den nya registrerade modellen som skapats av det här kommandot och som används av andra CLI-kommandon som fungerar med registrerade modeller.

Utdata för det här kommandot liknar följande JSON:

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

### <a name="model-versioning"></a>Versionshantering av modell

Observera att versionsnumret returneras för modellen. Versionen ökas varje gång du registrerar en ny modell med det här namnet. Du kan till exempel hämta modellen och registrera den från en lokal fil med hjälp av följande kommandon:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Det första kommandot hämtar den registrerade modellen till den aktuella katalogen. Filnamnet är `sklearn_mnist_model.pkl`, vilket är den fil som refererades när du registrerade modellen. Det andra kommandot registrerar den`-p "sklearn_mnist_model.pkl"`lokala modellen ( ) med`mymodel`samma namn som den tidigare registreringen ( ). Den här gången visar JSON-data som returneras versionen som 2.

## <a name="deploy-the-model"></a>Distribuera modellen

Om du vill distribuera en modell använder du följande kommando:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Du kan få en varning om "Det gick inte att kontrollera LocalWebservice-förekomsten" eller "Det gick inte att skapa Docker-klienten". Du kan ignorera detta på ett säkert sätt eftersom du inte distribuerar en lokal webbtjänst.

Det här kommandot distribuerar `myservice`en ny tjänst med namnet , med version 1 av modellen som du registrerade tidigare.

Filen `inferenceConfig.yml` innehåller information om hur du använder modellen för inferens. Den refererar till exempel till`score.py`inmatningsskriptet ( ) och programvaruberoenden.

Mer information om filens struktur finns [i konfigurationsschemat för inferens](reference-azure-machine-learning-cli.md#inference-configuration-schema). Mer information om inmatningsskript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

Beskriver `aciDeploymentConfig.yml` distributionsmiljön som används för att vara värd för tjänsten. Distributionskonfigurationen är specifik för den beräkningstyp som du använder för distributionen. I det här fallet används en Azure Container-instans. Mer information finns i [konfigurationsschemat för distribution.](reference-azure-machine-learning-cli.md#deployment-configuration-schema)

Det tar flera minuter innan distributionsprocessen är klar.

> [!TIP]
> I det här exemplet används Azure Container Instances. Distributioner till ACI skapar automatiskt den nödvändiga ACI-resursen. Om du i stället skulle distribuera till Azure Kubernetes Service måste du skapa ett `az ml model deploy` AKS-kluster i förväg och ange det som en del av kommandot. Ett exempel på distribution till AKS finns i [Distribuera en modell till ett Azure Kubernetes Service-kluster](how-to-deploy-azure-kubernetes-service.md).

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

### <a name="the-scoring-uri"></a>Poängsättningen URI

Den `scoringUri` returnerade från distributionen är REST-slutpunkten för en modell som distribueras som en webbtjänst. Du kan också hämta den här URI:n med följande kommando:

```azurecli-interactive
az ml service show -n myservice
```

Det här kommandot returnerar samma `scoringUri`JSON-dokument, inklusive .

REST-slutpunkten kan användas för att skicka data till tjänsten. Information om hur du skapar ett klientprogram som skickar data till tjänsten finns i [Använda en Azure Machine Learning-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Skicka data till tjänsten

Du kan skapa ett klientprogram för att anropa slutpunkten, men machine learning CLI tillhandahåller ett verktyg som kan fungera som en testklient. Använd följande kommando för att `testdata.json` skicka data i filen till tjänsten:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Om du använder PowerShell använder du följande kommando i stället:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Svaret från kommandot liknar `[ 3 ]`.

## <a name="clean-up-resources"></a>Rensa resurser

> [!IMPORTANT]
> Resurser som har skapats kan användas som förutsättningar för att andra självstudier och instruktionsartiklar om Azure Machine Learning.

### <a name="delete-deployed-service"></a>Ta bort distribuerad tjänst

Om du planerar att fortsätta använda arbetsytan Azure Machine Learning, men vill bli av med den distribuerade tjänsten för att minska kostnaderna, använder du följande kommando:

```azurecli-interactive
az ml service delete -n myservice
```

Det här kommandot returnerar ett JSON-dokument som innehåller namnet på den borttagna tjänsten. Det kan ta flera minuter innan tjänsten tas bort.

### <a name="delete-the-training-compute"></a>Ta bort träningsberäkningen

Om du planerar att fortsätta använda arbetsytan Azure Machine Learning, `cpu-cluster` men vill bli av med beräkningsmålet som skapats för utbildning, använder du följande kommando:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Det här kommandot returnerar ett JSON-dokument som innehåller ID för det borttagna beräkningsmålet. Det kan ta flera minuter innan beräkningsmålet har tagits bort.

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda de resurser du har skapat tar du bort dem så att du inte medför ytterligare avgifter.

Om du vill ta bort resursgruppen och alla Azure-resurser som skapats i det här dokumentet använder du följande kommando. Ersätt `<resource-group-name>` med namnet på resursgruppen som du skapade tidigare:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Nästa steg

I den här Azure Machine Learning-självstudien använde du MACHINE LEARNING CLI för följande uppgifter:

> [!div class="checklist"]
> * Installera maskininlärningstillägget
> * Skapa en Azure Machine Learning-arbetsyta
> * Skapa beräkningsresursen som används för att träna modellen
> * Definiera och registrera den datauppsättning som används för att träna modellen
> * Starta en träningsrunda
> * Registrera och ladda ner en modell
> * Distribuera modellen som en webbtjänst
> * Poängdata med hjälp av webbtjänsten

Mer information om hur du använder CLI finns i [Använda CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
