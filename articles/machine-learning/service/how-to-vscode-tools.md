---
title: Använd Visual Studio Code med
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
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818747"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Kom igång med Azure Machine Learning för Visual Studio Code

I den här artikeln får du lära dig hur du använder den **Azure Machine Learning för Visual Studio Code** tillägg för att träna och distribuera machine learning och deep learning-modeller med Azure Machine Learning-tjänsten i Visual Studio Code (VS Code).

Azure Machine Learning-tjänsten har stöd för att köra experiment lokalt och på fjärranslutna beräkningsmål. För varje experiment, du kan hålla reda på flera körningar som du behöver ofta upprepade gånger försöka olika tekniker och hyperparametrar. Du kan använda Azure Machine Learning för att spåra anpassade mått och experimentera körningar, aktiverar data science reproducerbarhet och revision.

Och du kan distribuera dessa modeller för dina behov för testning och produktion.

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

+ Visual Studio Code måste installeras. VS Code är en enkel men kraftfull källkodsredigerare som körs på skrivbordet. Den innehåller inbyggt stöd för Python med mera.  [Lär dig att installera VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installera Python 3.5 eller högre](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Installera Azure Machine Learning för VS Code-tillägg

När du installerar den **Azure Machine Learning** tillägg, två fler tillägg installeras automatiskt (om du har tillgång till internet). De är den [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) tillägg och [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) tillägg

Vi måste omvandla VS Code till en Python IDE för att fungera med Azure Machine Learning. Arbeta med [Python i Visual Studio Code](https://code.visualstudio.com/docs/languages/python), kräver Microsoft Python-tillägget som installeras automatiskt med Azure Machine Learning-tillägget. Tillägget gör VS Code en utmärkt IDE och fungerar på alla operativsystem med en mängd olika Python tolkar. Den använder alla VS Code förmåga att tillhandahålla automatisk komplettering och IntelliSense, ifyllningar, felsökning och Enhetstestning, samt möjlighet att enkelt växla mellan conda-miljöer och Python-miljöer, inklusive virtuella. Kolla in den här genomgången redigerar, körs, och felsökning Python-kod kan se den [Python Hello World-självstudier](https://code.visualstudio.com/docs/python/python-tutorial)

**Installera tillägget Azure Machine Learning:**

1. Starta VS Code.

1. Gå till i en webbläsare: [Azure Machine Learning för Visual Studio Code (förhandsversion)](https://aka.ms/vscodetoolsforai) tillägg

1. I den webbsidan klickar du på **installera**. 

1. Klicka på fliken tillägg **installera**.

1. En Välkommen flik öppnas i VS Code för tillägget och Azure symbolen (som beskrivs i den röda rutan i bilden nedan) läggs till i Aktivitetsfältet.

   ![Azure-ikonen i Aktivitetsfältet Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. I dialogrutan klickar du på **logga In** och följ anvisningarna på skärmen för att autentisera med Azure. 
   
   Tillägget Azure Account som installerades tillsammans med Azure Machine Learning för VS Code-tillägg, kan du autentisera med ditt Azure-konto. Se en lista över kommandon i den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) sidan.

> [!Tip] 
> Kolla in den [IntelliCode-tillägg för VS Code (förhandsversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode innehåller en uppsättning av AI-assisted funktioner för IntelliSense i Python, till exempel härledning av mest relevanta automatisk-slutförande baserat på den aktuella kontexten i kod.

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK Installation

1. Se till att Python 3.5 eller högre är installerad och identifieras av VS Code. Om du installerar den nu starta om VS Code och välj en Python-tolk med hjälp av anvisningarna i https://code.visualstudio.com/docs/python/python-tutorial.

1. I fönstret för den integrera terminalen, ange Python-tolk att använda eller du kan träffa **RETUR** du använder din standard Python-tolk.

   ![Välj vilken tolk](./media/vscode-tools-for-ai/python.png)

1. I det nedre högra hörnet i fönstret visas ett meddelande som anger att SDK: N för Azure ML är installeras automatiskt.    En lokal privat Python-miljö skapas som kräver Visual Studio Code för att arbeta med Azure Machine Learning-tjänsten.

   ![installera Azure Machine Learning SDK för Python](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Kom igång med Azure Machine Learning

Innan du börjar utbildning och distribuera maskininlärningsmodeller med VS Code, måste du skapa en [Azure Machine Learning-tjänstens arbetsyta](concept-azure-machine-learning-architecture.md#workspace) i molnet för att innehålla dina modeller och resurser. Lär dig hur du skapar en och skapa ditt första experiment på arbetsytan.

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure Machine Learning-sidopanelen visas.

   [![Installera](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Högerklicka på din Azure-prenumeration och välj **Skapa arbetsyta**. En lista visas. Prenumerationens namn är ”kostnadsfri utvärdering” i animerade avbildningen, och arbetsytan som är ”TeamWorkspace”. 

1. Välj en befintlig resursgrupp i listan eller skapa en ny med hjälp av guiden i Kommandopaletten.

1. Ange ett unikt och rensa namn för den nya arbetsytan i fältet. I skärmbilderna heter arbetsytan ”TeamWorkspace”.

1. Tryck på RETUR och den nya arbetsytan har skapats. Den visas i trädet nedan prenumerationens namn.

1. Högerklicka på noden Experiment och välj **skapa Experiment** på snabbmenyn.  Experiment Håll koll på dina körningar med hjälp av Azure Machine Learning.

1. I fältet kan du ange ett namn för experimentet. I skärmbilderna heter experimentet ”MNIST”.
 
1. Tryck på RETUR och nytt experiment skapas. Den visas i trädet nedan arbetsytans namn.

1. Du kan högerklicka på ett Experiment i en arbetsyta och Välj Ställ in som aktiv Experiment. Den **”aktiv”** experimentet är experimentet du använder och öppna mappen i VS Code kommer att länkas till det här experimentet i molnet. Den här mappen ska innehålla din lokala Python-skript.

   Nu körs var och en av ditt experiment med ditt experiment, så att alla viktiga mått sparas i historiken experiment och modeller du tränar får automatiskt överförs till Azure Machine Learning och lagras med dina experiment mått och loggar.

   [![Bifoga en mapp i VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>Skapa och hantera beräkningsmål

Med Azure Machine Learning för VS Code kan du förbereda dina data, skapa modeller och distribuera dem både lokalt och på fjärranslutna beräkningsmål.

Det här tillägget har stöd för flera olika remote beräkningsmål för Azure Machine Learning. Se den [fullständig lista över stöds beräkningsmål](how-to-set-up-training-targets.md) för Azure Machine Learning.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Skapa beräkningsmål för Azure Machine Learning i VS Code

**Skapa ett beräkningsmål:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure Machine Learning-sidopanelen visas.

2. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. Prenumerationens namn är ”kostnadsfri utvärdering” i animerade avbildningen, och arbetsytan som är ”TeamWorkspace”. 

3. Under noden arbetsyta högerklickar du på den **Compute** noden och välj **skapa Compute**.

4. Välj Beräkningstyp för mål i listan. 

5. I Kommandopaletten, väljer du en storlek på virtuell dator.

6. I Kommandopaletten, anger du ett namn för beräkningsmål i fältet. 

7. Ange avancerade egenskaper i JSON-konfigurationsfilen som öppnas i en ny flik. Du kan ange egenskaper, till exempel ett högsta antal noder.

8. När du är klar konfigurerar din beräkningsmål, klickar du på **skicka** i det nedre högra hörnet på skärmen.

Här är ett exempel för att skapa en Azure Machine Learning Compute (AMLCompute): [![Skapa AML-beräkning i VS Code](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Filen ”kör configuration”

VS Code-tillägg automatiskt skapar en lokal beräkningsmål och kör konfigurationer för dina **lokala** och **docker** miljöer på din lokala dator. Kör configuration-filer kan hittas under associerade mål Beräkningsnoden. 

## <a name="train-and-tune-models"></a>Träna och finjustera modeller

Använda Azure Machine Learning för VS Code (förhandsversion) för att snabbt iterera din kod, gå igenom och felsöka och använda källa kod kontroll lösning för jobbschemaläggning. 

**Att köra experimentet lokalt med Azure Machine Learning:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. 

1. Under noden arbetsytan Expandera den **Compute** noden och högerklicka på den **Kör Config** av beräkning du vill använda. 

1. Välj **kör Experiment**.

1. Välj skriptet som ska köras från Utforskaren. 

1. Klicka på **visa Experiment kör** att se den integrera Azure Machine Learning-portalen för att övervaka dina körningar och se dina tränade modeller.

Här är ett exempel för att köra ett experiment lokalt: [![Kör ett experiment lokalt](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Använd remote beräkningarna för experiment i VS Code

Om du vill använda en fjärransluten beräkningsmål vid utbildning, måste du skapa en konfigurationsfil för körning. Den här filen talar om för Azure Machine Learning inte bara om du vill köra experimentet men även hur du förbereder miljön.

#### <a name="the-conda-dependencies-file"></a>Filen conda-beroenden

Som standard en ny conda-miljö skapas åt dig och din av installationsberoenden hanteras. Dock måste du ange dina beroenden och deras versioner i den `aml_config/conda_dependencies.yml` filen. 

Det här är ett kodfragment från standard aml_config/conda_dependencies.yml. Du kan till exempel ange ”tensorflow = 1.12.0' enligt nedan. Om du inte anger versionen av beroendet används den senaste versionen.  
Du kan lägga till ytterligare beroenden i konfigurationsfilen.

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

**Om du vill köra ditt experiment med Azure Machine Learning på en fjärransluten beräkningsmålet:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn. 

1. Högerklicka på python-skriptet i fönstret redigeraren och välja **AML: Kör som-Experiment i Azure**. 

1. Välj målobjektet för beräkning i Kommandopaletten. 

1. I Kommandopaletten, anger du körningskonfiguration namn i fältet. 

1. Redigera filen conda_dependencies.yml för att ange den experiment körningsberoenden och klicka sedan på **skicka** i det nedre högra hörnet på skärmen. 

1. Klicka på **visa Experiment kör** att se den integrera Azure Machine Learning-portalen för att övervaka dina körningar och se dina tränade modeller.

Här är ett exempel för att köra ett experiment på en fjärransluten beräkningsmål: [![Kör ett experiment på en fjärransluten mål](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>Distribuera och hantera modeller
Azure Machine Learning gör det möjligt att distribuera och hantera dina machine learning-modeller i molnet och på gränsen. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Registrera din modell till Azure Machine Learning från VS Code

Nu när du har tränats din modell, kan du registrera den i din arbetsyta.
Registrerade modeller kan spåras och distribueras.

**Registrera din modell:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och Azure Machine Learning-tjänstens arbetsyta i trädvyn.

1. Under noden arbetsyta högerklickar du på **modeller** och välj **registrera modellen**.

1. I Kommandopaletten, anger du ett namn i fältet. 

1. I listan, väljer om du vill ladda upp en **modellfilen** (för enskild modeller) en **modellen mappen** (för modeller med flera filer, till exempel Tensorflow). 

1. Välj din mapp eller fil.

1. När du är klar konfigurerar din modellegenskaper, klickar du på **skicka** i det nedre högra hörnet på skärmen. 

Här är ett exempel för att registrera din modell till AML: [![Registrera en AML-modell](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>Distribuera tjänsten från VS Code

Med VS Code kan distribuera du din webbtjänst för att:
+ Azure Container Instance (ACI): för testning
+ Azure Kubernetes Service (AKS): för produktion 

Du behöver inte skapa en ACI-behållare för att testa i förväg eftersom de har skapats i farten. AKS-kluster måste dock vara konfigurerad i förväg. 

Läs mer om [distribution med Azure Machine Learning](how-to-deploy-and-where.md) i allmänhet.

**För att distribuera en webbtjänst:**

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure Machine Learning-sidopanelen visas.

1. Expandera din Azure-prenumeration och din arbetsyta för Azure Machine Learning-tjänsten i trädvyn.

1. Under noden arbetsytan Expandera den **modeller** noden.

1. Högerklicka på modellen som du vill distribuera till och välj **distribuera tjänst från registrerade modellen** från snabbmenyn.

1. I Kommandopaletten, välja beräkningsmål som ska distribueras i listan. 

1. I Kommandopaletten, anger du ett namn för den här tjänsten i fältet.  

1. Tryck på RETUR-tangenten på tangentbordet för att bläddra och välja skriptfilen i Kommandopaletten.

1. Tryck på RETUR-tangenten på tangentbordet för att bläddra och välj filen conda-beroenden i Kommandopaletten.

1. När du är klar konfigurerar din egenskaper, klickar du på **skicka** i det nedre högra hörnet på skärmen för att distribuera. Du kan ange en lokal Docker-fil eller en schema.json-fil som du kanske vill använda i den här tjänsten egenskaper för filen.

Webbtjänsten har nu distribuerats.

Här är ett exempel för att distribuera en webbtjänst: [![Distribuera en webbtjänst](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Använda kortkommandon

Som de flesta av VS Code är Azure Machine Learning-funktioner i VS Code tillgängliga från tangentbordet. Viktigaste tangentkombinationen veta är Ctrl + Skift + P, vilket ger dig Kommandopaletten. Härifrån kan har du åtkomst till alla funktioner i VS Code, inklusive kortkommandon för de vanligaste åtgärderna.

[![Kortkommandon för Azure Machine Learning för VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nästa steg

En genomgång av utbildning med Machine Learning utanför VS Code läser [självstudien: Träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).

En genomgång av redigering, köra och felsöka kod lokalt, finns i den [Python Hello World-självstudier](https://code.visualstudio.com/docs/python/python-tutorial)
