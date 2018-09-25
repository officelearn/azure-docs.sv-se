---
title: Installera och använda CLI för viktiga aktiviteter – Azure Machine Learning
description: Lär dig mer om att installera och använda CLI för de vanligaste machine learning-aktiviteter i Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 06e85845d41b240638a5b5b4d75d64fd460a99bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953335"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Installera och använda de machine learning CLI för viktiga aktiviteter i Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Azure Machine Learning-tjänsten är en integrerad, slutpunkt till slutpunkt för datavetenskap och avancerad analyslösning. Datatekniker kan använda Azure Machine Learning-tjänsten för att förbereda data, utveckla experiment och distribuera modeller i molnskala. 

Azure Machine Learning ger ett kommandoradsgränssnitt (CLI) som du kan:
+ Hantera din arbetsyta och projekt
+ Konfigurera beräkningsmål
+ Köra utbildning experiment
+ Visa historik och mått för senaste körningar
+ Distribuera modeller till produktion som web services
+ Hantera modeller i produktion och tjänster

Den här artikeln beskriver vi några av de mest användbara CLI-kommandona för din bekvämlighet. 

![Azure Machine Learning CLI](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Vad du behöver att komma igång

Du behöver deltagarbehörighet åtkomst till en Azure-prenumeration eller resursgrupp där du kan distribuera dina modeller. Dessutom måste du installera Azure Machine Learning Workbench för att kunna köra CLI. 

>[!IMPORTANT]
>CLI levereras med Azure Machine Learning-tjänsten skiljer sig från den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), som används för att hantera Azure-resurser.

## <a name="get-and-start-cli"></a>Hämta och starta CLI

För att få det här CLI, installera Azure Machine Learning Workbench, som du kan hämta härifrån:
    + Windows – https://aka.ms/azureml-wb-msi 
    + MacOS – https://aka.ms/azureml-wb-dmg 

Starta CLI:
+ Starta CLI på menyn i Azure Machine Learning Workbench **Arkiv -> Öppna Kommandotolken.**

## <a name="get-command-help"></a>Få hjälp om kommandot 

Du kan få extra information om CLI-kommandon med hjälp av `--debug` eller `--help` efter kommandona som `az ml <xyz> --debug` där `<xyz>` är kommandonamnet. Exempel:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Vanliga CLI-åtgärder för Azure Machine Learning 

Lär dig mer om de vanligaste uppgifterna som du kan utföra med CLI i det här avsnittet, inklusive:
+ [Konfigurera beräkningsmål](#target)
+ [Skicka jobb för fjärrkörning](#jobs)
+ [Arbeta med Jupyter-anteckningsböcker](#jupyter)
+ [Interagera med körhistorik](#history)
+ [Konfigurera din miljö för att operationalisera](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Konfigurera ett beräkningsmål

Du kan beräkna din machine learning-modell i olika mål, inklusive:
+ i lokalt läge
+ i Data Science VM (DSVM)
+ på ett HDInsight-kluster

Att koppla en virtuell dator för datavetenskap mål:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Att koppla ett HDInsight-mål:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

I den **aml_config** mappen, du kan ändra conda-beroenden. 

Du kan också arbeta med PySpark, Python eller Python i en GPU DSVM. 

Definiera arbetsläge Python:
+ För Python, lägga till `Framework:Python` i `<target name>.runconfig` 

+ PySpark, lägger du till `Framework:PySpark` i `<target name>.runconfig` 

+ För Python i en GPU DSVM
    1. Lägg till `Framework:Python` i `<target name>.runconfig` 

    1. Lägg även till `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` i `<target name>.compute`

Så här förbereder beräkningsmål:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>För att visa din prenumeration:<br/>
>`az account show`<br/>
>
>Att ställa in din prenumeration:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Skicka fjärrstyrda jobb

Att skicka ett jobb till en fjärransluten mål:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Arbeta med Jupyter-anteckningsböcker

Starta en Jupyter-anteckningsbok:
```azurecli
az ml notebook start
```

Detta kommando startar en Jupyter-anteckningsbok i localhost. Du kan arbeta i lokala genom att välja kernel Python 3, eller arbeta i en fjärransluten virtuell dator genom att välja kernel `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interagera med och utforska körningshistorik

Att visa körningshistoriken:
```azurecli
az ml history list -o table
```

Visa en lista över alla slutförda körningar:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Körs med bäst noggrannhet för att hitta:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Du kan också hämta de filer som skapas för varje körning. 

Uppgradera en modell som sparas i mappen utdata:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Så här hämtar du den modellen:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Konfigurera din miljö för att operationalisera

Om du vill konfigurera operationaliseringsmiljön måste du skapa:

> [!div class="checklist"]
> * En resursgrupp 
> * Ett lagringskonto
> * Ett Azure Container Registry (ACR)
> * Ett konto för Application insight
> * En Kubernetes-distribution på ett Azure Container Service (ACS)-kluster


Så här skapar en lokal distribution för testning i en Docker-behållare:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Så här skapar ett ACS-kluster med Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Så här övervakar statusen för distributionen:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Ange den miljö som ska användas:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Nästa steg

Kom igång med någon av följande artiklar: 
+ [Installera och börja använda Azure Machine Learning](quickstart-installation.md)
+ [Klassificera Iris-Data-självstudie: Del 1](tutorial-classifying-iris-part-1.md)

Fördjupa dig i någon av följande artiklar:
+ [CLI-kommandon för att hantera modeller](model-management-cli-reference.md)
