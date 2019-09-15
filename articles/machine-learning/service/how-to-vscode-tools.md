---
title: Använda Visual Studio Code för Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur du installerar Azure Machine Learning för Visual Studio Code och skapar ett enkelt experiment i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0507080a390a4bb9f981b0d911961337e9b2c62a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997184"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Kom igång med Azure Machine Learning för Visual Studio Code

I den här artikeln får du lära dig hur du använder tillägget för Azure Machine Learning för Visual Studio Code för att träna och distribuera maskin inlärnings-och djup inlärnings modeller.

[Azure Machine Learning](overview-what-is-azure-ml.md) ger stöd för experiment som du kör lokalt och på fjärranslutna beräknings mål. För varje experiment, du kan hålla reda på flera körningar som du behöver ofta upprepade gånger försöka olika tekniker och hyperparametrar. Du kan använda Azure Machine Learning för att spåra anpassade mått och experimentera körningar, aktiverar data science reproducerbarhet och revision.

Du kan också distribuera dessa modeller för testnings-och produktions behoven.

## <a name="prerequisites"></a>Förutsättningar

+ Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

+ Visual Studio Code måste vara installerat. Visual Studio Code är en lätt men kraftfull käll kods redigerare som körs på Skriv bordet. Det innehåller inbyggt stöd för python och andra programmeringsspråk. Ta reda på [hur](https://code.visualstudio.com/docs/setup/setup-overview)om du inte redan har installerat Visual Studio Code.

+ [Installera Python 3,5 eller senare](https://www.anaconda.com/download/).


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>Installera tillägget för Azure Machine Learning för Visual Studio Code

När du installerar tillägget Azure Machine Learning installeras två fler tillägg automatiskt (om du har Internet åtkomst). De är tillägget [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) och [Microsoft python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Om du vill arbeta med Azure Machine Learning måste du aktivera Visual Studio Code i en python-Integrated Development Environment (IDE). Du behöver Microsoft python-tillägget för att använda [python i Visual Studio Code](https://code.visualstudio.com/docs/languages/python). Tillägget installeras automatiskt med Azure Machine Learning-tillägget. Tillägget gör Visual Studio Code till en utmärkt IDE och fungerar på alla operativ system med olika python-tolkar. Microsoft python-tillägget använder all kraften i Visual Studio Code för att tillhandahålla Autoavsluta, IntelliSense, luddfri, fel sökning och enhets testning. Med tillägget kan du också enkelt växla mellan python-miljöer, inklusive virtuella och Conda miljöer. Mer information om att redigera, köra och felsöka python-kod finns i [själv studie kursen om python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).

Så här installerar du Azure Machine Learning-tillägget:

1. Öppna Visual Studio Code.

1. I en webbläsare går du till [Azure Machine Learning för Visual Studio Code Extension (för hands version)](https://aka.ms/vscodetoolsforai).

1. På webb sidan väljer du **Installera**. 

1. På fliken tillägg väljer du **Installera**.

1. En välkomst flik för tillägget öppnas i Visual Studio Code och Azure-symbolen (visas i rött i följande skärm bild) läggs till i aktivitets fältet.

   ![Azure-ikonen i aktivitets fältet i Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. I dialog rutan väljer du **Logga in** och följer anvisningarna för att autentisera med Azure. 
   
   Tillägget Azure-konto, som har installerats tillsammans med Azure Machine Learning för kod tillägget för Visual Studio, hjälper dig att autentisera med ditt Azure-konto. En lista över kommandon finns på sidan för [tillägget Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Kolla in [IntelliCode-tillägget för Visual Studio Code (för hands version)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode innehåller en uppsättning AI-assisterade funktioner för IntelliSense i python, till exempel att härleda de mest relevanta autokompletteringarna baserat på den aktuella kod kontexten.

## <a name="install-the-azure-machine-learning-sdk"></a>Installera Azure Machine Learning SDK

1. Se till att python 3,5 eller senare är installerat och känns igen av Visual Studio Code. Om du installerar det nu startar du om Visual Studio Code och [väljer en python-tolk](https://code.visualstudio.com/docs/python/python-tutorial).

1. I det integrerade terminalfönstret anger du den python-tolk som ska användas. Eller Välj RETUR om du vill använda din standard-python-tolk.

   ![Välj tolken](./media/vscode-tools-for-ai/python.png)

1. I det nedre högra hörnet i fönstret visas ett meddelande som anger att [SDK för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) installeras automatiskt. Den nyskapade python-miljön är lokal och privat och har Visual Studio Code-krav för att arbeta med Azure Machine Learning.

   ![Installera Azure Machine Learning SDK för python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Kom igång med Azure Machine Learning

Innan du börjar träna och distribuerar maskin inlärnings modeller i Visual Studio Code, måste du skapa en [Azure Machine Learning arbets yta](concept-workspace.md) i molnet. Den här arbets ytan kommer att innehålla dina modeller och resurser. 

Så här skapar du en arbets yta och lägger till ditt första experiment:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

   [![Skapa en arbets yta](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Högerklicka på din Azure-prenumeration och välj **skapa arbets yta**. En lista visas. I den animerade exempel bilden är prenumerations namnet **kostnads fri utvärderings version**och arbets ytan är **TeamWorkspace**. 

1. Välj en resurs grupp i listan eller skapa en ny med hjälp av guiden i kommando paletten.

1. I fältet anger du ett unikt och klart namn för den nya arbets ytan. I exempel bilden heter arbets ytan **TeamWorkspace**.

1. Välj RETUR för att skapa den nya arbets ytan. Den visas i trädet under prenumerations namnet.

1. Högerklicka på **experiment** -noden och välj **Skapa experiment** på snabb menyn.  Experiment håller koll på dina körningar med Azure Machine Learning.

1. I fältet anger du ett namn på experimentet. I exempel skärmarna heter experimentet **MNIST**.
 
1. Välj RETUR för att skapa det nya experimentet. Experimentet visas i trädet under arbets ytans namn.

1. I en arbets yta kan du högerklicka på ett experiment för att ställa in det som det **aktiva** experimentet. Det **aktiva** experimentet är ditt nuvarande experiment. Din öppna mapp i Visual Studio Code kommer att länkas till det här experimentet i molnet. Den här mappen ska innehålla dina lokala Python-skript.

Nu kommer dina nyckel mått att lagras i experiment historiken. På samma sätt kommer de modeller du träna att laddas upp automatiskt till Azure Machine Learning och lagras tillsammans med experiment mått och loggar. 

[![Bifoga en mapp i Visual Studio Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Skapa och hantera beräkningsmål

Med Azure Machine Learning för Visual Studio Code kan du förbereda dina data, träna modeller och distribuera dem både lokalt och på fjärranslutna beräknings mål.

Tillägget stöder flera fjärrberäknings mål för Azure Machine Learning. Mer information finns i den fullständiga listan med beräknings [mål som stöds för Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Skapa beräknings mål för Azure Machine Learning i Visual Studio Code

Så här skapar du ett beräknings mål:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

2. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan. I följande exempel bild är prenumerations namnet **kostnads fri utvärderings version**och arbets ytan är **TeamWorkspace**. 

3. Under noden arbetsyta högerklickar du på den **Compute** noden och välj **skapa Compute**.

4. Välj Beräkningstyp för mål i listan. 

5. På paletten kommando väljer du storlek på virtuell dator.

6. På paletten kommando, i fältet, anger du ett namn för beräknings målet. 

7. Ange eventuella avancerade egenskaper i JSON-konfigurationsfilen som öppnas på en ny flik. Du kan ange egenskaper som till exempel maximalt antal noder.

8. När du är klar med konfigurationen av beräknings målet väljer du **Skicka**i det nedre högra hörnet i fönstret.

Här är ett exempel på hur du skapar ett Azure Machine Learning Compute (AMLCompute):

[![Skapa AML Compute i Visual Studio Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Kör konfigurations filen

Visual Studio Code-tillägget skapar automatiskt ett lokalt beräknings mål och kör konfigurationer för dina lokala och Docker-miljöer på den lokala datorn. Du hittar konfigurationsfilerna för körning under den associerade Compute-målnoden. 

## <a name="train-and-tune-models"></a>Träna och finjustera modeller

Använd Azure Machine Learning för Visual Studio Code (för hands version) för att snabbt iterera på din kod, gå igenom och felsöka och Använd din lösning för käll kods kontroll. 

Köra experimentet lokalt genom att använda Azure Machine Learning:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan. 

1. Under noden arbets yta expanderar du **Compute** -noden och högerklickar på **körnings konfigurationen** för den beräkning som du vill använda. 

1. Välj **kör Experiment**.

1. Välj det skript du vill köra i Utforskaren. 

1. Välj **Visa experiment kör** för att se den integrerade Azure Machine Learning portalen för att övervaka dina körningar och se dina utbildade modeller.

Här är ett exempel på hur du kör ett experiment lokalt:

[![Kör ett experiment lokalt](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>Använda fjärrberäkningar för experiment i Visual Studio Code

Om du vill använda ett fjärrberäknings mål för träning måste du skapa en körnings konfigurations fil. Den här filen talar om för Azure Machine Learning inte bara om du vill köra experimentet men även hur du förbereder miljön.

#### <a name="the-conda-dependencies-file"></a>Filen conda-beroenden

Som standard skapas en ny Conda-miljö för dig, och dina installations beroenden hanteras. Du måste dock ange dina beroenden och deras versioner i filen *aml_config/conda_dependencies. yml* . 

Följande kodfragment från standard *aml_config/conda_dependencies. yml* anger `tensorflow=1.12.0`. Om du inte anger versionen av beroendet kommer den senaste versionen att användas. Du kan lägga till ytterligare beroenden i konfigurationsfilen.

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

Så här kör du experimentet med Azure Machine Learning på ett fjärran slutet beräknings mål:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan. 

1. I redigerings fönstret högerklickar du på python-skriptet och väljer **AML: Kör som experiment i Azure**. 

1. På paletten kommando väljer du Compute-målet. 

1. På paletten kommando, i fältet, anger du namnet på körnings konfigurationen. 

1. Redigera filen *conda_dependencies. yml* för att ange experimentets körnings beroenden. I det nedre högra hörnet av fönstret väljer du **Skicka**. 

1. Välj **Visa experiment kör** för att se den integrerade Azure Machine Learning portalen för att övervaka dina körningar och se dina utbildade modeller.

Här är ett exempel på hur du kör ett experiment på ett fjärrberäknings mål:

[![Köra ett experiment på ett fjärrmål](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Distribuera och hantera modeller
I Azure Machine Learning kan du distribuera och hantera dina Machine Learning-modeller i molnet och på gränsen. 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Registrera din modell för Azure Machine Learning från Visual Studio Code

Nu när du har tränat din modell kan du registrera den i din arbets yta. Du kan spåra och distribuera registrerade modeller.

Så här registrerar du din modell:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och Azure Machine Learning arbets ytan.

1. Under noden arbetsyta högerklickar du på **modeller** och välj **registrera modellen**.

1. På paletten kommando, i fältet, anger du ett modell namn. 

1. I listan väljer du om du vill ladda upp en **modell fil** (för enskilda modeller) eller en **modell mapp** (för modeller med flera filer, till exempel TensorFlow). 

1. Välj din mapp eller fil.

1. När du är klar med att konfigurera modell egenskaperna väljer du **Skicka**i det nedre högra hörnet i fönstret. 

Här är ett exempel på hur du registrerar din modell för att Azure Machine Learning:

[![Registrera en modell på AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>Distribuera din tjänst från Visual Studio Code

I Visual Studio Code kan du distribuera din webb tjänst till:
+ Azure Container Instances (ACI) för testning.
+ Azure Kubernetes service (AKS) för produktion.

Du behöver inte skapa en ACI-behållare för att testa i förväg eftersom ACI-behållare skapas i farten. Du måste dock konfigurera AKS-kluster i förväg. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Så här distribuerar du en webb tjänst:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code. Sid panelen för Azure Machine Learning visas.

1. I trädvyn expanderar du din Azure-prenumeration och din Azure Machine Learning-arbetsyta.

1. Under noden arbetsytan Expandera den **modeller** noden.

1. Högerklicka på den modell som du vill distribuera och välj **distribuera tjänst från registrerad modell** på snabb menyn.

1. På paletten kommando väljer du det beräknings mål som du vill distribuera till. 

1. På paletten kommando, i fältet, anger du ett namn för den här tjänsten.  

1. I paletten kommando väljer du nyckeln Enter på tangent bordet för att bläddra efter och välja skript filen.

1. På paletten kommando väljer du nyckeln Enter på tangent bordet för att bläddra efter och välja Conda beroende fil.

1. När du är klar med konfigurationen av tjänst egenskaperna väljer du **Skicka** för att distribuera i det nedre högra hörnet i fönstret. I tjänstens egenskaps fil kan du ange en lokal Docker-fil eller en schema. JSON-fil.

Webbtjänsten har nu distribuerats.

Här är ett exempel på hur du distribuerar en webb tjänst:

[![Distribuera en webb tjänst](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Använda kortkommandon

Du kan använda tangent bordet för att få åtkomst till Azure Machine Learning funktioner i Visual Studio Code. Det viktigaste kortkommandot att känna till är Ctrl + Shift + P, som visar paletten för kommandon. Från paletten kommando har du åtkomst till alla funktioner i Visual Studio Code, inklusive kortkommandon för de vanligaste åtgärderna.

[![Kortkommandon för Azure Machine Learning för Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nästa steg

* En genom gång av hur du tränar med Azure Machine Learning utanför Visual Studio Code finns i [Självstudier: Träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).
* En genom gång av hur du kan redigera, köra och felsöka kod lokalt finns i [själv studie kursen om python Hello-World](https://code.visualstudio.com/docs/python/python-tutorial).
