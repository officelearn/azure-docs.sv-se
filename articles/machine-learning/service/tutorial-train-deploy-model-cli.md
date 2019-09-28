---
title: Träna och distribuera modeller från CLI
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Machine Learning-tillägget för Azure CLI för att träna, registrera och distribuera en modell från kommando raden.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb46aaf04535c1b44cdd80810fbb6382dc727a67
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350415"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Självstudier: Träna och distribuera en modell från CLI

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

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)kan CLI nås via webbläsaren och finns i molnet.

## <a name="download-the-example-project"></a>Ladda ned exempel projektet

I den här självstudien hämtar du [https://github.com/microsoft/MLOps -](https://github.com/microsoft/MLOps) projektet. Filerna i `model-training`-och `model-deployment`-kataloger används av stegen i den här självstudien.

Hämta en lokal kopia av filerna genom att antingen [Hämta ett. zip-arkiv](https://github.com/microsoft/MLOps/archive/master.zip)eller använda följande git-kommando för att klona lagrings platsen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Träna filer

Katalogen `model-training` innehåller följande filer, som används när du tränar en modell:

* `.azureml\sklearn.runconfig`: En __körnings konfigurations__ fil. Den här filen definierar den körnings miljö som behövs för att träna modellen.
* `train-sklearn.py`: Övnings skriptet. Den här filen tränar modellen.
* `mylib.py`: En Helper-modul som används av `train-sklearn.py`.
* `training-env.yml`: Definierar de program beroenden som krävs för att köra utbildnings skriptet.

Utbildnings skriptet använder diabetes-datauppsättningen som medföljer scikit – lär dig att träna en modell.

### <a name="deployment-files"></a>Distributions filer

Katalogen `model-deployment` innehåller följande filer, som används för att distribuera den utbildade modellen som en webb tjänst:

* `aciDeploymentConfig.yml`: En __distributions konfigurations__ fil. Den här filen definierar den värd miljö som behövs för modellen.
* `inferenceConfig.yml`: En configuration__-fil för en härledning. Den här filen definierar den program miljö som används av tjänsten för att räkna data med modellen.
* `score.py`: Ett Python-skript som godkänner inkommande data, visar det med modellen och returnerar sedan ett svar.
* `scoring-env.yml`: De Conda-beroenden som krävs för att köra modellen och `score.py`-skript.

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

En resurs grupp är en grundläggande behållare för resurser på Azure-plattformen. När du arbetar med tjänsten Azure Machine Learning kommer resurs gruppen att innehålla arbets ytan för Azure Machine Learnings tjänsten. Den kommer också att innehålla andra Azure-tjänster som används av arbets ytan. Om du till exempel tränar din modell med en molnbaserad beräknings resurs skapas den resursen i resurs gruppen.

Använd följande kommando för att __skapa en ny resurs grupp__. Ersätt `<resource-group-name>` med det namn som ska användas för den här resurs gruppen. Ersätt `<location>` med den Azure-region som ska användas för den här resurs gruppen:

> [!TIP]
> Välj en region där Azure Machine Learnings tjänsten är tillgänglig. Mer information finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

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

Från en terminal-eller kommando tolk använder du följande kommandon för att ändra kataloger till katalogen `mlops` och ansluter sedan till din arbets yta:

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

Det här kommandot skapar en `.azureml/config.json`-fil som innehåller information som behövs för att ansluta till din arbets yta. Resten av de `az ml`-kommandon som används i den här självstudien använder den här filen, så du behöver inte lägga till arbets ytan och resurs gruppen i alla kommandon.

## <a name="create-the-compute-target-for-training"></a>Skapa Compute-målet för utbildning

I det här exemplet används en Azure Machine Learning beräknings instans för att träna modellen. Om du vill skapa en ny beräknings instans använder du följande kommando:

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
> Namnet på beräknings målet (`cpu` i det här fallet) är viktigt. den refereras till av `.azureml/sklearn.runconfig`-filen som används i nästa avsnitt.

## <a name="submit-the-training-run"></a>Skicka in utbildnings körningen

Om du vill starta en utbildning i `cpu`-Compute Target ändrar du kataloger till `model-training`-katalogen och använder sedan följande kommando:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Det här kommandot anger ett namn på experimentet (`myexperiment`). Experimentet lagrar information om den här körningen på arbets ytan.

Parametern `-c sklearn` anger `.azureml/sklearn.runconfig`-filen. Som tidigare nämnts innehåller den här filen information som används för att konfigurera miljön som används av tränings körningen. Om du inspekterar den här filen ser du att den refererar till det `cpu` Compute Target som du skapade tidigare. Det visar också antalet noder som ska användas när utbildningen (`"nodeCount": "4"`) och innehåller ett avsnitt med @no__t 1 som visar de python-paket som krävs för att köra övnings skriptet.

Mer information om att köra konfigurationsfiler finns i [Konfigurera och använda Compute-mål för modell träning](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli).

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

Den här texten loggas från övnings skriptet (`train-sklearn.py`) och visar två prestanda mått för den här modellen. I det här fallet vill vi ha modellen med det högsta alfa värdet. Prestanda måtten är speciella för den modell som du tränar. Andra modeller kommer att ha olika prestanda mått.

Om du inspekterar `train-sklearn.py`, ser du att den också använder det alpha-värdet när den har lagrat en eller flera av de utbildade modellerna i filen. I det här fallet tränar den flera modeller. Den som har högst alfa bör vara det bästa alternativet. Titta på utdata ovan och koden, modellen med en alfa på 0,95 sparades som `./outputs/ridge_0.95.pkl`

Modellen sparades i katalogen `./outputs` på det beräknings mål där den tränades. I det här fallet är Azure Machine Learning beräknings instansen i Azure-molnet. Inlärnings processen laddar automatiskt upp innehållet i `./outputs`-katalogen från beräknings målet där inlärning sker till Azure Machine Learning-arbetsytan. Den lagras som en del av experimentet (`myexperiment` i det här exemplet).

## <a name="register-the-model"></a>Registrera modellen

Om du vill registrera modellen direkt från den lagrade versionen i experimentet använder du följande kommando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Det här kommandot registrerar `outputs/ridge_0.95.pkl`-filen som skapats av träningen och körs som en ny modell registrering med namnet `mymodel`. @No__t-0 refererar till en sökväg i ett experiment. I det här fallet läses experimentet och körnings informationen in från `runoutput.json`-filen som skapas av övnings kommandot. @No__t-0 skapar en JSON-fil som refererar till den nya registrerade modellen som skapats av det här kommandot och används av andra CLI-kommandon som fungerar med registrerade modeller.

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

Det första kommandot laddar ned den registrerade modellen till den aktuella katalogen. Fil namnet är `ridge_0.95.pkl`, som är den fil som refereras när du registrerade modellen. Det andra kommandot registrerar den lokala modellen (`-p "ridge_0.95.pkl"`) med samma namn som den tidigare registreringen (`mymodel`). Den här gången visar de JSON-data som returnerade versionen som 2.

## <a name="deploy-the-model"></a>Distribuera modellen

Om du vill distribuera en modell ändrar du kataloger till katalogen `model-deployment` och använder sedan följande kommando:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Du kan få meddelandet "Det gick inte att skapa Docker-klienten". Du kan ignorera det här meddelandet. CLI kan distribuera en webb tjänst till en lokal Docker-behållare och söker efter Docker. I det här fallet använder vi inte en lokal distribution.

Det här kommandot distribuerar en ny tjänst med namnet `myservice`, med version 1 av den modell som du registrerade tidigare.

@No__t-0-filen innehåller information om hur du utför en härledning, till exempel Entry-skriptet (`score.py`) och program varu beroenden. Mer information om strukturen för den här filen finns i schemat för [konfiguration av energischemat](reference-azure-machine-learning-cli.md#inference-configuration-schema). Mer information om Entry-skript finns i [Distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md#prepare-to-deploy).

@No__t-0 beskriver distributions miljön som används som värd för tjänsten. Distributions konfigurationen är speciell för den beräknings typ som du använder för distributionen. I det här fallet används en Azure Container instance. Mer information finns i [konfigurations schema för distribution](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

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

Den `scoringUri` som returnerades från distributionen är en REST-slutpunkt för en modell som distribueras som en webb tjänst. Du kan också få denna URI genom att använda följande kommando:

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

Om du planerar att fortsätta använda Azure Machine Learning arbets ytan, men vill ta bort det `cpu`-beräknings mål som skapats för utbildning, använder du följande kommando:

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

Mer information om hur du använder CLI finns i [använda CLI-tillägget för Azure Machine Learning-tjänsten](reference-azure-machine-learning-cli.md).