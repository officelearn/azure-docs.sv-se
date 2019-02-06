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
ms.openlocfilehash: e6da35eeb98d4017baae6aa2c8e19fa3f9d5ac1b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752576"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Kom igång med Azure Machine Learning för Visual Studio Code

I den här artikeln får du lära dig hur du installerar den **Azure Machine Learning för Visual Studio Code** tillägg och skapa ditt första experiment med Azure Machine Learning-tjänsten i Visual Studio Code (VS Code).

Använd Azure Machine Learning-tillägg i Visual Studio code för att använda Azure Machine Learning-tjänsten att Förbered dina data, träna och testa machine learning-modeller på lokala och fjärranslutna beräkningsmål, distribuerar modellerna och spåra anpassade mått och experiment.

## <a name="prerequisites"></a>Förutsättningar


+ Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](http://aka.ms/AMLFree) i dag.

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

1. En Välkommen flik öppnas i VS Code för tillägget och Azure symbolen läggs till i Aktivitetsfältet.

   ![Azure-ikonen i Aktivitetsfältet Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. I dialogrutan klickar du på **logga In** och följ anvisningarna på skärmen för att autentisera med Azure. 
   
   Tillägget Azure Account som installerades tillsammans med Azure Machine Learning för VS Code-tillägg, kan du autentisera med ditt Azure-konto. Se en lista över kommandon i den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) sidan.

> [!Tip] 
> Kolla in den [IntelliCode-tillägg för VS Code (förhandsversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode innehåller en uppsättning av AI-assisted funktioner för IntelliSense i Python, till exempel härledning av mest relevanta automatisk-slutförande baserat på den aktuella kontexten i kod.

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK Installation

1. Se till att Python 3.5 eller högre är installerad och identifieras av VS Code. Om du installerar den nu starta om VS Code och välj en Python-tolk med hjälp av anvisningarna i https://code.visualstudio.com/docs/python/python-tutorial.

1. I fönstret för den integrera terminalen, ange Python-tolk att använda eller du kan träffa **RETUR** du använder din standard Python-tolk.

   ![Välj vilken tolk](./media/vscode-tools-for-ai/python.png)

1. I det nedre högra hörnet i fönstret visas ett meddelande som anger att SDK: N för Azure ML är installeras automatiskt.    En lokal privat Python-miljö skapas med Visual Studio Code förutsättningarna för att arbeta med Azure Machine Learning.

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

### <a name="use-keyboard-shortcuts"></a>Använda kortkommandon

Som de flesta av VS Code är Azure Machine Learning-funktioner i VS Code tillgängliga från tangentbordet. Viktigaste tangentkombinationen veta är Ctrl + Skift + P, vilket ger dig Kommandopaletten. Härifrån kan har du åtkomst till alla funktioner i VS Code, inklusive kortkommandon för de vanligaste åtgärderna.

[![Kortkommandon för Azure Machine Learning för VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nästa steg

Du kan nu använda Visual Studio Code för att arbeta med Azure Machine Learning.

Lär dig hur du [skapa beräkningsmål, träna och distribuera modeller i Visual Studio Code](how-to-vscode-train-deploy.md).
