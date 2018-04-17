---
title: Installera och använda CLI för viktiga aktiviteter - Azure Machine Learning
description: Lär dig hur du installerar och använder CLI för de vanligaste uppgifterna i Azure Machine Learning för maskininlärning.
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
ms.openlocfilehash: 33a1665c8f09efae88c831172199fca3e0b7634d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Installerar och använder maskininlärning CLI för viktiga aktiviteter i Azure Machine Learning

Azure Machine Learning-tjänster är en integrerad, slutpunkt-till-slutpunkt datavetenskap och avancerade analyser lösning. Professional datavetare kan använda Azure Machine Learning services och förbereda data, utveckla experiment och distribuera modeller i molnskala. 

Azure Machine Learning ger kommandoradsgränssnittet (CLI) som du kan:
+ Hantera arbetsytan och projekt
+ Konfigurera beräkning mål
+ Kör utbildning experiment
+ Visa historik och mått för senaste körs
+ Distribuera modeller till produktionen som webbtjänster
+ Hantera tillverkningen och tjänster

Den här artikeln beskriver några av de mest användbara CLI-kommandona för din bekvämlighet. 

![Azure Machine Learning CLI](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Vad du behöver att komma igång

Du behöver deltagarbehörighet åtkomst till en Azure-prenumeration eller resursgrupp där du kan distribuera modeller. Du måste också installera Azure Machine Learning-arbetsstationen för att kunna köra CLI. 

>[!IMPORTANT]
>CLI med Azure Machine Learning tjänster skiljer sig från den [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest), som används för att hantera Azure-resurser.

## <a name="get-and-start-cli"></a>Hämta och starta CLI

Installera Azure Machine Learning Workbench, som kan hämtas från den här för att få den här CLI:
    + Windows – https://aka.ms/azureml-wb-msi 
    + MacOS- https://aka.ms/azureml-wb-dmg 

Så här startar CLI:
+ Starta CLI på menyn i Azure Machine Learning arbetsstationen **Arkiv -> Öppna en kommandotolk.**

## <a name="get-command-help"></a>Få hjälp med kommandot 

Du kan få ytterligare information om CLI-kommandon med hjälp av `--debug` eller `--help` efter kommandona som `az ml <xyz> --debug` där `<xyz>` är kommandonamnet. Exempel:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Vanliga CLI uppgifter för Azure Machine Learning 

Lär dig mer om de vanligaste uppgifterna som du kan utföra med CLI i det här avsnittet, inklusive:
+ [Konfigurera beräkning mål](#target)
+ [Skickar jobb för fjärrkörning](#jobs)
+ [Arbeta med Jupyter-anteckningsböcker](#jupyter)
+ [Interaktion med kör historik](#history)
+ [Konfigurera din miljö för att operationalisera](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Ställ in ett beräknings-mål

Du kan beräkna din maskininlärning modell i olika mål, inklusive:
+ i lokalt läge
+ i en datavetenskap VM (DSVM)
+ på ett HDInsight-kluster

Bifoga datavetenskap VM mål:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Att koppla ett HDInsight-mål:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

I den **aml_config** mappen, du kan ändra conda beroenden. 

Dessutom kan du arbeta med PySpark, Python eller Python i en GPU DSVM. 

Definiera arbetsläge Python:
+ För Python, lägga till `Framework:Python` i `<target name>.runconfig` 

+ För PySpark, lägga till `Framework:PySpark` i `<target name>.runconfig` 

+ För Python i en GPU-DSVM
    1. Lägg till `Framework:Python` i `<target name>.runconfig` 

    1. Lägg även till `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` i `<target name>.compute`

Så här förbereder beräkning målet:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Så här visar du din prenumeration:<br/>
>`az account show`<br/>
>
>Så här prenumerationen:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Skicka remote jobb

Att skicka en jobbet till en fjärransluten mål:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Arbeta med Jupyter-anteckningsböcker

Starta en Jupyter-anteckningsbok:
```azurecli
az ml notebook start
```

Detta kommando startar en Jupyter-anteckningsbok i localhost. Du kan arbeta i lokala genom att välja kernel Python 3 eller arbeta i den virtuella datorn med fjärråtkomst genom att välja kernel `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interagera med och utforska körningshistoriken

Listan körningshistoriken:
```azurecli
az ml history list -o table
```

Så här visar alla slutförda körs:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Körs med bästa noggrannhet att söka efter:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Du kan också hämta de filer som skapas för varje körning. 

Att befordra en modell som sparas i mappen utdata:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Hämta den modellen:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Konfigurera din miljö för att operationalisera

Om du vill konfigurera din operationalization miljö måste du skapa:

> [!div class="checklist"]
> * En resursgrupp 
> * ett lagringskonto
> * En Azure-behållaren registret (ACR)
> * Ett program insight konto
> * En Kubernetes distribution i ett kluster med Azure Container Service (ACS)


Att konfigurera en lokal distribution för testning i en dockerbehållare:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Att ställa in en ACS-kluster med Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Så här övervakar statusen för distributionen:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Att ställa in miljön som ska användas:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Nästa steg

Kom igång med något av följande artiklar: 
+ [Installera och börja använda Azure Machine Learning](quickstart-installation.md)
+ [Klassificera Iris Data Självstudier: Del 1](tutorial-classifying-iris-part-1.md)

Gräva djupare med något av följande artiklar:
+ [CLI-kommandon för att hantera modeller](model-management-cli-reference.md)