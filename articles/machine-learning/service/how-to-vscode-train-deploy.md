---
title: Använd Visual Studio Code för att träna och distribuera modeller med Azure Machine Learning-tjänsten
description: Läs mer om Azure Machine Learning för Visual Studio Code och hur du startar utbildning och distribuera machine learning och deep learning-modeller i Azure Machine Learning-tjänsten med hjälp av Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec12
ms.openlocfilehash: e1ea0b867738f72d9ff4baff25b630887b9232c9
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016444"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>Använd Visual Studio Code för att träna och distribuera machine learning-modeller

I den här artikeln får du lära dig hur du använder den **Azure Machine Learning för Visual Studio Code** tillägg för att träna och distribuera machine learning och deep learning-modeller med Azure Machine Learning-tjänsten i Visual Studio Code (VS Code).

Azure Machine Learning har stöd för att köra experiment lokalt och på fjärranslutna beräkningsmål. För varje experiment, du kan hålla reda på flera körningar som du behöver ofta upprepade gånger försöka olika tekniker och hyperparametrar. Du kan använda Azure Machine Learning för att spåra anpassade mått och experimentera körningar, aktiverar data science reproducerbarhet och revision.

Och du kan distribuera dessa modeller för dina behov för testning och produktion.

## <a name="prerequisites"></a>Förutsättningar

+ Har den [Azure Machine Learning för VS Code](how-to-vscode-tools.md) tillägget ställa in.

+ Har den [Azure Machine Learning-SDK för Python installerat](how-to-vscode-tools.md) med VS Code.

+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLfree) innan du börjar.

## <a name="create-and-manage-compute-targets"></a>Skapa och hantera beräkningsmål

Med Azure Machine Learning för VS Code kan du förbereda dina data, skapa modeller och distribuera dem både lokalt och på fjärranslutna beräkningsmål.

Det här tillägget har stöd för flera olika remote beräkningsmål för Azure Machine Learning. Se den [fullständig lista över stöds beräkningsmål](how-to-set-up-training-targets.md) för Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Skapa beräkningsmål för Azure Machine Learning i VS Code

**Skapa ett beräkningsmål:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure: Machine Learning sidopanelen visas.

2. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. Prenumerationens namn är ”kostnadsfri utvärdering” i animerade avbildningen, och arbetsytan som är ”TeamWorkspace”. 

3. Under noden arbetsyta högerklickar du på den **Compute** noden och välj **skapa Compute**.

4. Välj Beräkningstyp för mål i listan. 

5. Ange avancerade egenskaper i JSON-konfigurationsfilen som öppnas i en ny flik. Du kan ange ett unikt namn för beräkningsmål i den här filen.

6. När du är klar konfigurerar din beräkningsmål, klickar du på **skicka** längst ned till höger.

Här är ett exempel för Azure Machine Learning Compute (AMLCompute): [ ![skapa AML Compute i VS Code](./media/vscode-tools-for-ai/CreateAMLCompute.gif)](./media/vscode-tools-for-ai/CreateAMLCompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Använd remote beräkningarna för experiment i VS Code

Om du vill använda en fjärransluten beräkningsmål vid utbildning, måste du skapa en konfigurationsfil för körning. Den här filen talar om för Azure Machine Learning inte bara om du vill köra experimentet men även hur du förbereder miljön.

#### <a name="the-run-configuration-file"></a>Filen ”kör configuration”

VS Code-tillägg kommer automatiskt att skapa en körningskonfiguration för din **lokala** och **docker** miljöer på din lokala dator.

Det här är ett kodfragment från standardvärdet kör konfigurationsfilen.

Om du vill installera alla bibliotek/beroenden själv anger `userManagedDependencies: True` och sedan lokala experimentkörningar använder din standardmiljö för Python enligt VS Code Python-tillägg.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

#### <a name="the-conda-dependencies-file"></a>Filen conda-beroenden

Som standard en ny conda-miljö skapas åt dig och din av installationsberoenden hanteras. Du måste dock ange dina beroenden i den `aml_config/conda_dependencies.yml` filen.

Det här är ett kodfragment från standard aml_config/conda_dependencies.yml.
Du kan lägga till ytterligare beroenden i konfigurationsfilen.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Träna och finjustera modeller

Använda Azure Machine Learning för VS Code (förhandsversion) för att snabbt iterera din kod, gå igenom och felsöka och använda källa kod kontroll lösning för jobbschemaläggning. 

**Köra ditt experiment med Azure Machine Learning:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure: Machine Learning sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. 

1. Under noden arbetsytan Expandera den **Compute** nod och högerklicka på den **Kör Config** av beräkning du vill använda. 

1. Välj **kör Experiment**.

1. Klicka på **visa Experiment kör** att se den integrera Azure Machine Learning-portalen för att övervaka dina körningar och se dina tränade modeller.

## <a name="deploy-and-manage-models"></a>Distribuera och hantera modeller
Azure Machine Learning gör det möjligt att distribuera och hantera dina machine learning-modeller i molnet och på gränsen. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrera din modell till Azure Machine Learning från VS Code

Nu när du har tränats din modell, kan du registrera den i din arbetsyta.
Registrerade modeller kan spåras och distribueras.

**Registrera din modell:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure: Machine Learning sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn.

1. Under noden arbetsyta högerklickar du på **modeller** och välj **registrera modellen**.

1. I listan, väljer om du vill ladda upp en **modellfilen** (för enskild modeller) en **modellen mappen** (för modeller med flera filer, till exempel Tensorflow). 

1. Välj din mapp eller fil.

1. När du är klar konfigurerar din modellegenskaper, klickar du på **skicka** längst ned till höger. 



### <a name="deploy-your-service-from-vs-code"></a>Distribuera tjänsten från VS Code

Med VS Code kan distribuera du din webbtjänst för att:
+ Azure Container Instance (ACI): för testning
+ Azure Kubernetes Service (AKS): för produktion 

Du behöver inte skapa en ACI-behållare för att testa i förväg eftersom de har skapats i farten. AKS-kluster måste dock vara konfigurerad i förväg. 

Läs mer om [distribution med Azure Machine Learning](how-to-deploy-and-where.md) i allmänhet.

**För att distribuera en webbtjänst:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure: Machine Learning sidopanelen visas.

1. Expandera din Azure-prenumeration och din arbetsyta för Azure Machine Learning-tjänsten i trädvyn.

1. Under noden arbetsytan Expandera den **modeller** noden.

1. Högerklicka på modellen som du vill distribuera till och välj **distribuera tjänst från registrerade modellen** från snabbmenyn.

1. I Kommandopaletten, välja beräkningsmål som ska distribueras i listan. 

1. I fältet, anger du ett namn för den här tjänsten. 

1. Tryck på RETUR-tangenten på tangentbordet för att bläddra och välja skriptfilen i Kommandopaletten.

1. Tryck på RETUR-tangenten på tangentbordet för att bläddra och välj filen conda-beroenden i Kommandopaletten.

1. När du är klar konfigurerar din egenskaper, klickar du på **skicka** längst ned till höger. Du kan ange en lokal Docker-fil eller en schema.json-fil som du kanske vill använda i den här tjänsten egenskaper för filen.

Webbtjänsten har nu distribuerats.

## <a name="next-steps"></a>Nästa steg

En genomgång av utbildning med Machine Learning utanför VS Code läser [självstudie: skapa modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).

En genomgång av redigering, köra och felsöka kod lokalt, finns i den [Python Hello World-självstudier](https://code.visualstudio.com/docs/python/python-tutorial)
