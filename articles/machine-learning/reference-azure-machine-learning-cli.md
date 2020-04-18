---
title: CLI-förlängning
titleSuffix: Azure Machine Learning
description: Lär dig mer om AZURE Machine Learning CLI-tillägget för Azure CLI. Azure CLI är ett plattformsoberoende kommandoradsverktyg som gör att du kan arbeta med resurser i Azure-molnet. Med machine learning-tillägget kan du arbeta med Azure Machine Learning. ML CLI skapar och hanterar resurser som arbetsyta, datalager, datauppsättningar, pipelines, modeller och distributioner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618072"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Använda CLI-tillägget för Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI är ett tillägg till [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), ett kommandoradsgränssnitt över flera plattformar för Azure-plattformen. Det här tillägget innehåller kommandon för att arbeta med Azure Machine Learning. Det gör att du kan automatisera dina maskininlärningsaktiviteter. Följande lista innehåller några exempel på åtgärder som du kan göra med CLI-tillägget:

+ Kör experiment för att skapa maskininlärningsmodeller

+ Registrera maskininlärningsmodeller för kundanvändning

+ Paketera, distribuera och spåra livscykeln för dina maskininlärningsmodeller

CLI ersätter inte Azure Machine Learning SDK. Det är ett kompletterande verktyg som är optimerat för att hantera mycket parameteriserade uppgifter som passar sig väl till automatisering.

## <a name="prerequisites"></a>Krav

* Om du vill använda CLI måste du ha en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Om du använder [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)nås CLI via webbläsaren och finns i molnet.

## <a name="full-reference-docs"></a>Fullständig referensdokument

Hitta [de fullständiga referensdokumenten för azure-cli-ml-tillägget av Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Anslut CLI till din Azure-prenumeration

> [!IMPORTANT]
> Om du använder Azure Cloud Shell kan du hoppa över det här avsnittet. Molnskalet autentiserar automatiskt dig med det konto du loggar in på din Azure-prenumeration.

Det finns flera sätt som du kan autentisera till din Azure-prenumeration från CLI. Det mest grundläggande är att interaktivt autentisera med hjälp av en webbläsare. Om du vill autentisera interaktivt öppnar du en kommandorad eller terminal och använder följande kommando:

```azurecli-interactive
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa instruktionerna på kommandoraden. Instruktionerna innebär att du [https://aka.ms/devicelogin](https://aka.ms/devicelogin) surfar på och anger en auktoriseringskod.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Andra metoder för autentisering finns [i Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installera tillägget

Så här installerar du CLI-tillägget Machine Learning:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Exempel filer som du kan använda med kommandona nedan kan hittas [här](https://aka.ms/azml-deploy-cloud).

När du `y` uppmanas till det väljer du att installera tillägget.

Om du vill kontrollera att tillägget har installerats använder du följande kommando för att visa en lista över ML-specifika underkommandon:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Uppdatera tillägget

Så här uppdaterar du CLI-tillägget Machine Learning:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Ta bort tillägget

Om du vill ta bort CLI-tillägget använder du följande kommando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resurshantering

Följande kommandon visar hur du använder CLI för att hantera resurser som används av Azure Machine Learning.

+ Om du inte redan har en skapar du en resursgrupp:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Skapa en Azure Machine Learning-arbetsyta:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Det här kommandot skapar en grundläggande arbetsyta för utgåvan. Om du vill skapa en `--sku enterprise` företagsarbetsyta använder du växeln `az ml workspace create` med kommandot. Mer information om Azure Machine Learning-versioner finns i [Vad är Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Mer information finns i [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Koppla en arbetsytas konfiguration till en mapp för att aktivera CLI-kontextuell medvetenhet.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Det här kommandot `.azureml` skapar en underkatalog som innehåller exempelkörnings- och conda-miljöfiler. Den innehåller också `config.json` en fil som används för att kommunicera med din Azure Machine Learning-arbetsyta.

    Mer information finns i [az ml-mapprena](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Bifoga en Azure-blob-behållare som ett datalager.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Mer information finns i [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Ladda upp filer till ett datalager.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Mer information finns i [uppladdningen av az ml-datalager](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Koppla ett AKS-kluster som ett beräkningsmål.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Mer information finns i [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Skapa ett nytt AMLcompute-mål.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Mer information finns i [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Kör experiment

* Starta en körning av experimentet. När du använder det här kommandot anger du namnet på \*runconfig-filen (texten före .runconfig om du tittar på filsystemet) mot parametern -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Kommandot `az ml folder attach` skapar en `.azureml` underkatalog, som innehåller två exempel runconfig filer. 
    >
    > Om du har ett Python-skript som skapar ett körningskonfigurationsobjekt programmässigt kan du använda [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) för att spara det som en runconfig-fil.
    >
    > Det fullständiga runconfig-schemat finns i den här [JSON-filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schemat är självdokumenterande via `description` nyckeln för varje objekt. Dessutom finns det uppräknare för möjliga värden och ett mallkodavsnitt i slutet.

    Mer information finns i [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Visa en lista över experiment:

    ```azurecli-interactive
    az ml experiment list
    ```

    Mer information finns i [experimentlistan az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Hantering av datauppsättningar

Följande kommandon visar hur du arbetar med datauppsättningar i Azure Machine Learning:

+ Registrera en datauppsättning:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Information om formatet på den JSON-fil som används `az ml dataset register --show-template`för att definiera datauppsättningen använder du .

    Mer information finns i [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Arkivera en aktiv eller föråldrad datauppsättning:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Mer information finns i [az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Inaktuell datauppsättning:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Mer information finns i [az ml dataset deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Lista alla datauppsättningar på en arbetsyta:

    ```azurecli-interactive
    az ml dataset list
    ```

    Mer information finns i [az ml-datauppsättningslistan](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Få information om en datauppsättning:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Mer information finns i [az ml-datauppsättningen](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Återaktivera en arkiverad eller föråldrad datauppsättning:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Mer information finns i [az ml-datauppsättningen återaktivera](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Avregistrera en datauppsättning:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Mer information finns i [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Miljöledning

Följande kommandon visar hur du skapar, registrerar och listar Azure Machine [Learning-miljöer](how-to-configure-environment.md) för din arbetsyta:

+ Skapa byggnadsställningar för en miljö:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Mer information finns i [az ml-miljöställning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrera en miljö:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Mer information finns i [miljöregistret az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Lista registrerade miljöer:

    ```azurecli-interactive
    az ml environment list
    ```

    Mer information finns i [listan över miljöer i AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Ladda ner en registrerad miljö:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Mer information finns i [az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schema för konfiguration av miljö

Om du `az ml environment scaffold` använde kommandot genereras en `azureml_environment.json` mallfil som kan ändras och användas för att skapa anpassade miljökonfigurationer med CLI. Objektet på den översta nivån [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) mappar löst till klassen i Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

I följande tabell beskrivs varje fält på den översta nivån i JSON-filen, dess typ och en beskrivning. Om en objekttyp är länkad till en klass från Python SDK finns det en lös 1:1-matchning mellan varje JSON-fält och det offentliga variabelnamnet i klassen Python. I vissa fall kan fältet mappas till ett konstruktorargument i stället för en klassvariabel. `environmentVariables` Fältet mappar till exempel `environment_variables` till variabeln [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) i klassen.

| JSON-fältet | Typ | Beskrivning |
|---|---|---|
| `name` | `string` | Miljöns namn. Börja inte namnges med **Microsoft** eller **AzureML**. |
| `version` | `string` | Version av miljön. |
| `environmentVariables` | `{string: string}` | En hash-karta över miljövariabelnamn och värden. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objekt som definierar Python-miljön och tolken som ska användas på målberäkningsresursen. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definierar inställningar för att anpassa Docker-avbildningen som är byggd enligt miljöns specifikationer. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | Avsnittet konfigurerar Spark-inställningar. Det används endast när ramen är inställd på PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Konfigurerar Beroenden för Databricks bibliotek. |
| `inferencingStackVersion` | `string` | Anger den inferencing stack version som lagts till i bilden. Lämna det här fältet `null`om du vill undvika att lägga till en inferencingstapel . Giltigt värde: "senaste". |

## <a name="ml-pipeline-management"></a>HANTERING AV ML-rörledningar

Följande kommandon visar hur du arbetar med machine learning-pipelines:

+ Skapa en pipeline för maskininlärning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Mer information finns i [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Mer information om YAML-filen för pipeline finns [i Definiera pipelines för maskininlärning i YAML](reference-pipeline-yaml.md).

+ Kör en pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Mer information finns i [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Mer information om YAML-filen för pipeline finns [i Definiera pipelines för maskininlärning i YAML](reference-pipeline-yaml.md).

+ Schemalägg en pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Mer information finns i [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Mer information om YAML-filen för pipelineschema finns [i Definiera pipelines för maskininlärning i YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Modellregistrering, profilering, distribution

Följande kommandon visar hur du registrerar en tränad modell och distribuerar den sedan som en produktionstjänst:

+ Registrera en modell med Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Mer information finns i [az ml-modellregistret](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **VALFRITT** Profilera din modell för att få optimala CPU- och minnesvärden för distribution.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Mer information finns i [az ml-modellprofilen](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Distribuera din modell till AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Mer information om inferenskonfigurationsfilschemat finns [i Inferenskonfigurationsschema](#inferenceconfig).
    
    Mer information om distributionskonfigurationsfilschemat finns i [Konfigurationsschema för distribution](#deploymentconfig).

    Mer information finns i [az ml-modellut distribuera](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schema för inferenskonfiguration

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Konfigurationsschema för distribution

### <a name="local-deployment-configuration-schema"></a>Konfigurationsschema för lokal distribution

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Konfigurationsschema för Azure Container Instance-distribution 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Konfigurationsschema för Azure Kubernetes-tjänstdistribution

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Nästa steg

* [Kommandoreferens för MACHINE Learning CLI-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Träna och distribuera maskininlärningsmodeller med Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
