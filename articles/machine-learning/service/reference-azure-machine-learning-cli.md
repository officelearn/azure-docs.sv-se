---
title: Om Azure Machine Learning CLI-tillägg
description: Läs mer om de machine learning CLI-tillägg för Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 7e430d1b590413f497c851b687abcaa98e04d0e4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053869"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Vad är Azure Machine Learning CLI?

Det är Azure Machine Learning kommandoradsgränssnitt (CLI)-tillägget för dataexperter och utvecklare som arbetar med Azure Machine Learning-tjänsten. Det kan du automatisera arbetsflöden för machine learning snabbt och sätt ihop dem till produktion, till exempel:
+ Köra experiment för att skapa machine learning-modeller

+ Registrera maskininlärningsmodeller för kundens användning

+ Paketera, distribuera och spåra livscykeln för din machine learning-modeller

Den här machine learning CLI är en utökning av [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) och har byggts ovanpå Python-baserade <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> för Azure Machine Learning-tjänsten.

> [!NOTE]
> CLI är en tidig förhandsversion och kommer att uppdateras.

## <a name="installing-and-uninstalling"></a>Installation och avinstallation

Du kan installera CLI med hjälp av det här kommandot från vår förhandsgranskning PyPi index:
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Du kan ta bort CLI med hjälp av det här kommandot:
```AzureCLI
az extension remove -n azure-cli-ml
```

Du kan uppdatera CLI med den **ta bort** och **lägga till** stegen ovan.

## <a name="using-the-cli-vs-the-sdk"></a>Med hjälp av CLI jämfört med SDK
CLI är bättre lämpat för automation genom en devops-person eller som en del av en kontinuerlig integrering och leverans pipeline. Det är optimerat för att hantera ovanliga och mycket parametriserade uppgifter. 

Exempel:
- Compute-etablering
- Skicka parametriserade experiment
- registrering av modellen, skapa avbildningar
- Distributionen av tjänsten

Dataexperter rekommenderas att använda Azure ML-SDK.

## <a name="common-machine-learning-cli-commands"></a>Vanliga CLI-kommandon för maskininlärning

Använd den omfattande uppsättningen med `az ml` kommandon för att interagera tjänsten i alla kommandoradmiljö, inklusive Azure-portalen molnskalet.

Här är ett exempel på vanliga kommandon:

### <a name="workspace-creation--compute-setup"></a>Arbetsytans konfiguration för skapande och beräkning

+ Skapa en Azure Machine Learning-arbetsyta, den översta resursen för machine learning.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Ange CLI för att använda den här arbetsytan som standard.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Skapa en DSVM (data science VM) för utbildning-modeller. Du kan också skapa BatchAI kluster för distribuerad utbildning.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Skicka experiment
+ Ansluta till ett projekt (kör konfiguration) för att skicka ett experiment. Detta används för att hålla koll på dina experimentkörningar.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Skicka ett experiment mot Azure Machine Learning-tjänsten på beräkningsmål önskar. Det här exemplet körs mot din lokala beräkningsmiljö. Du kan hitta ett exempelskript train.py [här](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/train.py).

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ Visa en lista med skickade experiment.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Modeller registrering, bild ceation och distribution

+ Registrera en modell med Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m mymodel.pkl  -w myworkspace -g myresourcegroup
  ```

+ Skapa en avbildning som innehåller dina machine learning-modell och beroenden. 
  ```AzureCLI
  az ml image create -n myimage -r python -m mymodel.pkl -f score.py -c myenv.yml
  ```

+ Distribuera din paketerade modell till mål inklusive ACI och AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice -i myimage:1
  ```
    
## <a name="full-command-list"></a>Fullständig Kommandolistan
Du hittar en fullständig lista över kommandon för CLI-tillägg (och deras parametrar som stöds) genom att köra ```az ml COMMANDNAME -h```. 
