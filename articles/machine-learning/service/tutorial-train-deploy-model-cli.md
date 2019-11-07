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
ms.date: 09/12/2019
ms.openlocfilehash: 1854599956755716955a6e691c3266ac54ddafd9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581558"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Självstudie: träna och distribuera en modell från CLI
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien använder du Machine Learning-tillägget för Azure CLI för att träna, registrera och distribuera en modell.

I den här självstudien för python [-utbildning används scikit – lär dig](https://scikit-learn.org/) att träna en grundläggande modell. Den här självstudien fokuserar inte på skripten eller modellen, men processen för att använda CLI för att arbeta med Azure Machine Learning.

Läs hur du vidtar följande åtgärder:

> [!div class="checklist"]
> * Installera Machine Learning-tillägget
> * Skapa en Azure Machine Learning-arbetsyta
> * Skapa den beräknings resurs som används för att träna modellen
> * Starta en tränings körning
> * Registrera och ladda ned en modell
> * Distribuera modellen som en webb tjänst
> * Poäng data med hjälp av webb tjänsten

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)kan CLI nås via webbläsaren och finns i molnet.

## <a name="download-the-example-project"></a>Ladda ned exempel projektet

I den här självstudien hämtar du [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) -projektet. Filerna i `model-training` och `model-deployment` kataloger används av stegen i den här självstudien.

Hämta en lokal kopia av filerna genom att antingen [Hämta ett. zip-arkiv](https://github.com/microsoft/MLOps/archive/master.zip)eller använda följande git-kommando för att klona lagrings platsen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Träna filer

`model-training` katalogen innehåller följande filer, som används när du tränar en modell:

* `.azureml\sklearn.runconfig`: en __körnings konfigurations__ fil. Den här filen definierar den körnings miljö som behövs för att träna modellen.
* `train-sklearn.py`: utbildnings skriptet. Den här filen tränar modellen.
* `mylib.py`: en hjälp modul som används av `train-sklearn.py`.
* `training-env.yml`: definierar de program beroenden som krävs för att köra utbildnings skriptet.

Utbildnings skriptet använder diabetes-datauppsättningen som medföljer scikit – lär dig att träna en modell.

### <a name="deployment-files"></a>Distributions filer

`model-deployment`-katalogen innehåller följande filer, som används för att distribuera den utbildade modellen som en webb tjänst:

* `aciDeploymentConfig.yml`: en __distributions konfigurations__ fil. Den här filen definierar den värd miljö som behövs för modellen.
* `inferenceConfig.yml`: en configuration__-fil för en härledning. Den här filen definierar den program miljö som används av tjänsten för att räkna data med modellen.
* `score.py`: ett Python-skript som accepterar inkommande data, visar det med modellen och returnerar sedan ett svar.
* `scoring-env.yml`: de Conda-beroenden som behövs för att köra modellen och `score.py` skriptet.

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

Från en terminal-eller kommando tolk använder du följande kommandon för att ändra kataloger till `mlops` katalogen och ansluter sedan till din arbets yta:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Det här kommandot skapar en `.azureml/config.json`-fil som innehåller information som behövs för att ansluta till din arbets yta. Resten av de `az ml`-kommandon som används i den här självstudien kommer att använda den här filen, så du behöver inte lägga till arbets ytan och resurs gruppen i alla kommandon.

## <a name="create-the-compute-target-for-training"></a>Skapa Compute-målet för utbildning

I det här exemplet används en Azure Machine Learning Notebook VM för att träna modellen. Använd följande kommando för att skapa en ny virtuell dator i Notebook:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

Utdata från det här kommandot liknar följande JSON:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Det här kommandot skapar ett nytt Compute-mål med namnet `cpu`, med högst fyra noder. Den VM-storlek som valts tillhandahåller en virtuell dator med en GPU-resurs. Information om storleken på den virtuella datorn finns i [VM-typer och storlekar].

> [!IMPORTANT]
> Namnet på beräknings målet (`cpu` i det här fallet) är viktigt. den `.azureml/sklearn.runconfig`-fil som används i nästa avsnitt refereras till.

## <a name="submit-the-training-run"></a>Skicka in utbildnings körningen

Om du vill starta en utbildning som körs på `cpu` Compute Target, ändrar du kataloger till `model-training`-katalogen och använder sedan följande kommando:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Det här kommandot anger ett namn på experimentet (`myexperiment`). Experimentet lagrar information om den här körningen på arbets ytan.

Parametern `-c sklearn` anger `.azureml/sklearn.runconfig`s filen. Som tidigare nämnts innehåller den här filen information som används för att konfigurera miljön som används av tränings körningen. Om du inspekterar den här filen ser du att den refererar till `cpu` Compute Target som du skapade tidigare. Det visar också antalet noder som ska användas när utbildning (`"nodeCount": "4"`), och innehåller ett `"condaDependenciees"`-avsnitt som visar de python-paket som krävs för att köra övnings skriptet.

Mer information om att köra konfigurationsfiler finns i [Konfigurera och använda beräknings mål för modell träning](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli), eller referera till den här [JSON-filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) för att se hela schemat för en runconfig.

Parametern `-t` lagrar en referens till den här körningen i en JSON-fil och kommer att användas i nästa steg för att registrera och ladda ned modellen.

När inlärnings körnings processen strömmar den information från utbildningen på fjärrdatorns beräknings resurs. En del av informationen liknar följande text:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Den här texten loggas från utbildnings skriptet (`train-sklearn.py`) och visar två prestanda mått för den här modellen. I det här fallet vill vi ha modellen med det högsta alfa värdet. Prestanda måtten är speciella för den modell som du tränar. Andra modeller kommer att ha olika prestanda mått.

Om du inspekterar `train-sklearn.py`ser du att den också använder alfa värdet när du lagrar den utbildade modellen (erna) i filen. I det här fallet tränar den flera modeller. Den som har högst alfa bör vara det bästa alternativet. Titta på utdata ovan, och koden, modellen med en alfa på 0,95 sparades som `./outputs/ridge_0.95.pkl`

Modellen sparades i `./outputs`-katalogen på beräknings målet där den tränades. I det här fallet Azure Machine Learning VM Notebook i Azure-molnet. Inlärnings processen överför automatiskt innehållet i `./outputs`-katalogen från beräknings målet där inlärningen sker till Azure Machine Learning-arbetsytan. Den lagras som en del av experimentet (`myexperiment` i det här exemplet).

## <a name="register-the-model"></a>Registrera modellen

Om du vill registrera modellen direkt från den lagrade versionen i experimentet använder du följande kommando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Det här kommandot registrerar `outputs/ridge_0.95.pkl`-filen som skapats av träningen och körs som en ny modell registrering med namnet `mymodel`. `--assets-path` refererar till en sökväg i ett experiment. I det här fallet läses experimentet och körnings informationen in från `runoutput.json`-filen som skapats av övnings kommandot. `-t registeredmodel.json` skapar en JSON-fil som refererar till den nya registrerade modellen som skapats med det här kommandot och används av andra CLI-kommandon som fungerar med registrerade modeller.

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
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

Det första kommandot laddar ned den registrerade modellen till den aktuella katalogen. Fil namnet är `ridge_0.95.pkl`, vilket är den fil som refereras när du registrerade modellen. Det andra kommandot registrerar den lokala modellen (`-p "ridge_0.95.pkl"`) med samma namn som den tidigare registreringen (`mymodel`). Den här gången visar de JSON-data som returnerade versionen som 2.

## <a name="deploy-the-model"></a>Distribuera modellen

Om du vill distribuera en modell ändrar du kataloger till `model-deployment` katalogen och använder sedan följande kommando:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Du kan få meddelandet "Det gick inte att skapa Docker-klienten". Du kan ignorera det här meddelandet. CLI kan distribuera en webb tjänst till en lokal Docker-behållare och söker efter Docker. I det här fallet använder vi inte en lokal distribution.

Det här kommandot distribuerar en ny tjänst med namnet `myservice`, med version 1 av den modell som du registrerade tidigare.

`inferenceConfig.yml`-filen innehåller information om hur du kan utföra en härledning, till exempel start skriptet (`score.py`) och program beroenden. Mer information om strukturen för den här filen finns i schemat för [konfiguration av energischemat](reference-azure-machine-learning-cli.md#inference-configuration-schema). Mer information om Entry-skript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

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

Även om du kan skapa ett klient program för att anropa slut punkten, innehåller Machine Learning CLI ett verktyg som kan fungera som en test klient. Använd följande kommando för att skicka test data till tjänsten:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

Svaret från kommandot liknar `[4684.920839774082]`.

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

Om du planerar att fortsätta använda Azure Machine Learning arbets ytan, men vill ta bort `cpu` Compute-målet som skapats för utbildning, använder du följande kommando:

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
> * Starta en tränings körning
> * Registrera och ladda ned en modell
> * Distribuera modellen som en webb tjänst
> * Poäng data med hjälp av webb tjänsten

Mer information om hur du använder CLI finns i [Använd CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
