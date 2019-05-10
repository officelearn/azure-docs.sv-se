---
title: Använd Visual Studio Code för machine learning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du installerar Azure Machine Learning för Visual Studio Code och skapa ett enkelt experiment i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19873256f8253fff75cfd42df7b876106a9e98e5
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464796"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Kom igång med Azure Machine Learning för Visual Studio Code

I den här artikeln lär du dig att använda tillägget för Azure Machine Learning för Visual Studio Code för att träna och distribuera machine learning och modeller för djupinlärning med hjälp av Azure Machine Learning-tjänsten i Visual Studio Code.

Azure Machine Learning-tjänsten har stöd för experiment som du kör lokalt och på fjärranslutna beräkningsmål. För varje experiment, du kan hålla reda på flera körningar som du behöver ofta upprepade gånger försöka olika tekniker och hyperparametrar. Du kan använda Azure Machine Learning för att spåra anpassade mått och experimentera körningar, aktiverar data science reproducerbarhet och revision.

Du kan också distribuera dessa modeller för dina behov för testning och produktion.

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfri eller betald version av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree).

+ Visual Studio Code måste installeras. Visual Studio Code är en enkel men kraftfull källkodsredigerare som körs på skrivbordet. Den innehåller inbyggt stöd för Python och andra programmeringsspråk. Om du inte redan har installerat Visual Studio Code [ta reda på hur](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installera Python 3.5 eller senare](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installera tillägget för Azure Machine Learning för Visual Studio Code

När du installerar Azure Machine Learning-tillägget installeras automatiskt två fler tillägg (om du har tillgång till internet). De är den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) och [Microsoft Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Om du vill arbeta med Azure Machine Learning, måste du inaktivera Visual Studio Code en Python-integrerad utvecklingsmiljö (IDE). Du behöver Microsoft Python-tillägg för att använda [Python i Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Det här tillägget installeras automatiskt med Azure Machine Learning-tillägget. Tillägget gör Visual Studio Code en utmärkt IDE och den fungerar på alla operativsystem med en rad olika Python tolkar. Microsoft Python-tillägget använder alla kraften i Visual Studio Code för att tillhandahålla automatisk komplettering, IntelliSense, ifyllningar, felsökning och Enhetstestning. Tillägget kan du enkelt växla mellan Python-miljöer, inklusive virtuella och conda-miljöer. Mer information om hur du redigerar, köra och felsöka Python-kod, finns det [Python hello-world självstudien](https://code.visualstudio.com/docs/python/python-tutorial).

Installera tillägget Azure Machine Learning:

1. Öppna Visual Studio Code.

1. I en webbläsare går du till [Azure Machine Learning för Visual Studio Code-tillägg (förhandsversion)](https://aka.ms/vscodetoolsforai).

1. På sidan, Välj **installera**. 

1. På fliken tillägg väljer **installera**.

1. En Välkommen flik för tillägget öppnas i Visual Studio Code och Azure symbolen (som beskrivs i rött i skärmbilden) har lagts till i Aktivitetsfältet.

   ![Azure-ikonen i Aktivitetsfältet Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. I dialogrutan Välj **logga In** och följ anvisningarna för att autentisera med Azure. 
   
   Tillägget Azure Account som installerades tillsammans med Azure Machine Learning för Visual Studio Code-tillägg, kan du autentisera med ditt Azure-konto. En lista över kommandon finns på sidan för den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Kolla in den [IntelliCode tillägg för Visual Studio Code (förhandsversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode innehåller en uppsättning av AI-assisted funktioner för IntelliSense i Python, till exempel härledning av de mest relevanta ifyllningar baserat på den aktuella kontexten i kod.

## <a name="install-the-azure-machine-learning-sdk"></a>Installera Azure Machine Learning SDK

1. Se till att Python 3.5 eller senare har installerats och kan identifieras av Visual Studio Code. Om du installerar den nu måste du starta om Visual Studio Code och [väljer en Python-tolk](https://code.visualstudio.com/docs/python/python-tutorial).

1. I fönstret för den integrera terminalen, anger du Python-tolk att använda. Eller tryck på RETUR för att använda din standard Python-tolk.

   ![Välj vilken tolk](./media/vscode-tools-for-ai/python.png)

1. I det nedre högra hörnet i fönstret visas ett meddelande som anger att SDK: N för Azure Machine Learning är installeras automatiskt. Den nyligen skapade Python-miljön är lokala och privata och det kräver Visual Studio Code för att arbeta med Azure Machine Learning-tjänsten.

   ![Installera Azure Machine Learning SDK för Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Kom igång med Azure Machine Learning

Innan du börjar utbildning och distribuera machine learning-modeller i Visual Studio Code, måste du skapa en [Azure Machine Learning-tjänstens arbetsyta](concept-azure-machine-learning-architecture.md#workspace) i molnet. Den här arbetsytan innehåller dina modeller och resurser. 

Skapa en arbetsyta och lägga till ditt första experiment:

1. Välj Azure i fältet för Visual Studio Code-aktivitet. Azure Machine Learning-sidopanelen visas.

   [![Skapa en arbetsyta](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Högerklicka på din Azure-prenumeration och välj **Skapa arbetsyta**. En lista visas. Animerade exempelbild finns prenumerationens namn är **kostnadsfri utvärderingsversion**, och arbetsytan som är **TeamWorkspace**. 

1. Välj en resursgrupp i listan eller skapa en ny med hjälp av guiden i kommandopaletten.

1. Ange ett unikt och rensa namn för den nya arbetsytan i fältet. I exempelbild arbetsytan med namnet **TeamWorkspace**.

1. Tryck på RETUR för att skapa den nya arbetsytan. Den visas i trädet nedan prenumerationens namn.

1. Högerklicka på den **Experiment** nod, och välj **skapa Experiment** på snabbmenyn.  Experiment Håll koll på dina körningar med hjälp av Azure Machine Learning.

1. I fältet, anger du ett namn för experimentet. I exempel-skärmbilderna experimentet heter **MNIST**.
 
1. Välj RETUR för att skapa nya experiment. Experimentet visas i trädet nedan arbetsytans namn.

1. I en arbetsyta högerklickar du på ett experiment för att ange den som den **Active** experimentera. Den **Active** experimentet är ditt aktuella experiment. Öppna mappen i Visual Studio Code kommer att länkas till det här experimentet i molnet. Den här mappen ska innehålla din lokala Python-skript.

Nu lagras dina viktiga mått i historiken för experimentet. På samma sätt kan du träna modeller automatiskt överförs till Azure Machine Learning och lagras tillsammans med dina experiment mått och loggar. 

[![Bifoga en mapp i Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Skapa och hantera beräkningsmål

Med Azure Machine Learning för Visual Studio Code, kan du förbereda dina data, skapa modeller och distribuera dem både lokalt och på fjärranslutna beräkningsmål.

Tillägget har stöd för flera fjärranslutna beräkningsmål för Azure Machine Learning. Mer information finns i en fullständig lista över stöds [beräkningsmål för Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Skapa beräkningsmål för Azure Machine Learning i Visual Studio Code

Skapa ett beräkningsmål:

1. Välj Azure i fältet för Visual Studio Code-aktivitet. Azure Machine Learning-sidopanelen visas.

2. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. I följande exempelbild prenumerationens namn är **kostnadsfri utvärderingsversion**, och arbetsytan som är **TeamWorkspace**. 

3. Under noden arbetsyta högerklickar du på den **Compute** noden och välj **skapa Compute**.

4. Välj Beräkningstyp för mål i listan. 

5. Välj en storlek på virtuell dator på kommandopaletten.

6. Ange ett namn för beräkningsmål på kommandopaletten, i fältet. 

7. Ange avancerade egenskaper i konfigurationsfilen JSON som öppnas på en ny flik. Du kan ange egenskaper, till exempel ett högsta antal noder.

8. När du har konfigurerat din beräkningsmål i det nedre högra hörnet i fönstret, Välj **skicka**.

Här är ett exempel på hur du skapar en Azure Machine Learning-beräkning (AMLCompute):

[![Skapa AML-beräkning i Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Kör konfigurationsfilen

Visual Studio Code-tillägg skapar automatiskt en lokal beräkningsmål och kör konfigurationer för dina lokala och docker-miljöer på din lokala dator. Du hittar kör konfigurationsfilerna under associerade mål Beräkningsnoden. 

## <a name="train-and-tune-models"></a>Träna och finjustera modeller

Använda Azure Machine Learning för Visual Studio Code (förhandsversion) för att snabbt iterera din kod, gå igenom och felsöka och använda din lösning för källkodskontroll. 

Kör experimentet lokalt genom att använda Azure Machine Learning:

1. Välj Azure i fältet för Visual Studio Code-aktivitet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. 

1. Under noden arbetsytan Expandera den **Compute** nod och högerklicka på den **Kör Config** av beräkningarna som du vill använda. 

1. Välj **kör Experiment**.

1. Utforskaren, väljer du skriptet du vill köra. 

1. Välj **visa Experiment kör** att se den integrera Azure Machine Learning-portalen för att övervaka dina körningar och se dina tränade modeller.

Här är ett exempel på hur du kör ett experiment lokalt:

[![Kör ett experiment lokalt](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Använd remote beräkningarna för experiment i Visual Studio Code

Om du vill använda en fjärransluten beräkningsmål för träning, måste du skapa en konfigurationsfil för körning. Den här filen talar om för Azure Machine Learning inte bara om du vill köra experimentet men även hur du förbereder miljön.

#### <a name="the-conda-dependencies-file"></a>Filen conda-beroenden

Som standard en ny conda-miljö skapas åt dig och din av installationsberoenden hanteras. Dock måste du ange dina beroenden och deras versioner i den *aml_config/conda_dependencies.yml* fil. 

Följande kodavsnitt från standard *aml_config/conda_dependencies.yml* anger `tensorflow=1.12.0`. Om du inte anger versionen av beroendet, används den senaste versionen. Du kan lägga till ytterligare beroenden i konfigurationsfilen.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

Om du vill köra ditt experiment med Azure Machine Learning på en fjärransluten beräkningsmålet:

1. Välj Azure i fältet för Visual Studio Code-aktivitet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. 

1. Högerklicka på Python-skriptet i fönstret redigeraren och välj **AML: Kör som-Experiment i Azure**. 

1. Välj målobjektet för beräkning på kommandopaletten. 

1. Ange kör Konfigurationsnamnet på kommandopaletten, i fältet. 

1. Redigera den *conda_dependencies.yml* fil att ange den experiment körningsberoenden. I det nedre högra hörnet i fönstret väljer du **skicka**. 

1. Välj **visa Experiment kör** att se den integrera Azure Machine Learning-portalen för att övervaka dina körningar och se dina tränade modeller.

Här är ett exempel på hur du kör ett experiment på en fjärransluten beräkningsmål:

[![Kör ett experiment på en fjärransluten mål](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Distribuera och hantera modeller
Du kan distribuera och hantera dina machine learning-modeller i molnet och på gränsen i Azure Machine Learning. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrera din modell till Azure Machine Learning från Visual Studio Code

Nu när du har tränat modellen, kan du registrera den i din arbetsyta. Du kan spåra och distribuera modeller som är registrerade.

Registrera din modell:

1. Välj Azure i fältet för Visual Studio Code-aktivitet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn.

1. Under noden arbetsyta högerklickar du på **modeller** och välj **registrera modellen**.

1. Ange ett namn på kommandopaletten, i fältet. 

1. I listan, Välj om du vill ladda upp en **modellfilen** (för enskild modeller) eller en **modellen mappen** (för modeller med flera filer, till exempel TensorFlow). 

1. Välj din mapp eller fil.

1. När du har konfigurerat din modellegenskaper, i det nedre högra hörnet i fönstret, Välj **skicka**. 

Här är ett exempel på hur du registrerar din modell till Azure Machine Learning:

[![Registrera en AML-modell](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Distribuera tjänsten från Visual Studio Code

I Visual Studio Code kan du distribuera din webbtjänst för att:
+ Azure Container Instances (ACI) för testning.
+ Azure Kubernetes Service (AKS) för produktion.

Du behöver inte skapa en ACI-behållare för att testa i förväg, eftersom ACI behållare skapas i farten. Men behöver du konfigurera AKS-kluster i förväg. Mer information finns i [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

För att distribuera en webbtjänst:

1. Välj Azure i fältet för Visual Studio Code-aktivitet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och din arbetsyta för Azure Machine Learning-tjänsten i trädvyn.

1. Under noden arbetsytan Expandera den **modeller** noden.

1. Högerklicka på modellen som du vill distribuera och väljer **distribuera tjänst från registrerade modellen** på snabbmenyn.

1. På kommandopaletten, välja beräkningsmål som du vill distribuera till. 

1. Ange ett namn för den här tjänsten på kommandopaletten, i fältet.  

1. På kommandopaletten, väljer du RETUR-tangenten på tangentbordet för att söka efter och välj skriptfilen.

1. På kommandopaletten, väljer du RETUR-tangenten på tangentbordet för att söka efter och välj filen conda-beroenden.

1. När du har konfigurerat din tjänstegenskaper i det nedre högra hörnet i fönstret, Välj **skicka** att distribuera. Du kan ange en lokal docker-fil eller en schema.json-fil i filen service egenskaper.

Webbtjänsten har nu distribuerats.

Här är ett exempel på hur du distribuerar en webbtjänst:

[![Distribuera en webbtjänst](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Använda kortkommandon

Du kan använda tangentbordet för att få åtkomst till Azure Machine Learning-funktioner i Visual Studio Code. Viktigaste kortkommandot veta är Ctrl + Skift + P, som visar kommandopaletten. Från kommandopaletten har du åtkomst till alla funktioner i Visual Studio-kod, inklusive kortkommandon för de vanligaste åtgärderna.

[![Kortkommandon för Azure Machine Learning för Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nästa steg

* En genomgång av hur du tränar med Azure Machine Learning utanför Visual Studio Code finns i [självstudien: Träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).
* En genomgång av hur du redigerar, köra och felsöka kod lokalt finns i den [Python hello-world självstudien](https://code.visualstudio.com/docs/python/python-tutorial).
