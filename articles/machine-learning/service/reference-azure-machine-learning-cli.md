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
ms.openlocfilehash: 53e737f35904a90bb56ec15c8a8282f8775e3c3a
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393499"
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
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
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
> [!NOTE]
> Exempel på filer som du kan använda för att utföra den kommandona nedan hittar du [här.](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)

Använd den omfattande uppsättningen med `az ml` kommandon för att interagera tjänsten i alla kommandoradmiljö, inklusive Azure-portalen molnskalet.

Här är ett exempel på vanliga kommandon:

### <a name="workspace-creation--compute-setup"></a>Arbetsytans konfiguration för skapande och beräkning

+ Skapa en Azure Machine Learning-tjänsten arbetsyta och den översta resursen för machine learning.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Ange CLI för att använda den här arbetsytan som standard.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Skapa en DSVM (data science VM). Du kan också skapa BatchAI kluster för distribuerad utbildning eller AKS-kluster för distribution.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Skicka experiment
+ Ansluta till ett projekt (kör konfiguration) för att skicka ett experiment. Detta används för att hålla koll på dina experimentkörningar.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Skicka ett experiment mot Azure Machine Learning-tjänsten på beräkningsmål önskar. Det här exemplet körs mot din lokala beräkningsmiljö. Kontrollera att filen conda miljö samlar in python-beroenden.

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
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Skapa en avbildning som innehåller dina machine learning-modell och beroenden. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Distribuera din paketerade modell till mål inklusive ACI och AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>Fullständig Kommandolistan
Du hittar en fullständig lista över kommandon för CLI-tillägg (och deras parametrar som stöds) genom att köra ```az ml COMMANDNAME -h```. 
