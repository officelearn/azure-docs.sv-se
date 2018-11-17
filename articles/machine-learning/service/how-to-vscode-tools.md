---
title: Med hjälp av Visual Studio Code Tools för AI-tillägget med Azure Machine Learning
description: Läs mer om Visual Studio Code Tools för AI och hur du startar utbildning och distribuera machine learning och deep learning-modeller med Azure Machine Learning-tjänsten i VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 57fe511e5de0d73f2a372da0ecab3e9a3039b194
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854211"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>VS Code Tools för AI: Kom igång med Azure Machine Learning från Visual Studio Code

I den här artikeln får du lära dig om tillägg för Visual Studio Code (VS Code), **Tools för AI**, och hur du startar utbildning och distribuera machine learning och deep learning-modeller med Azure Machine Learning-tjänsten i VS Code.

Använd verktyg för AI-tillägg i Visual Studio code för att använda Azure Machine Learning-tjänsten till Förbered dina data, träna och testa ML-modeller på lokala och fjärranslutna beräkningsmål, distribuerar modellerna och spåra anpassade mått och experiment.

## <a name="prerequisite"></a>Förutsättning

+ Visual Studio Code måste installeras. VS Code är en enkel men kraftfull källkodsredigerare som körs på skrivbordet. Den innehåller inbyggt stöd för Python med mera.  [Lär dig att installera VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Installera Python 3.5 eller högre](https://www.anaconda.com/download/).

+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLfree) innan du börjar.

## <a name="install-vs-code-tools-for-ai-extension"></a>Installera VS Code Tools för AI-tillägg

När du installerar den **Tools för AI** tillägg, två fler tillägg installeras automatiskt (om du har tillgång till internet). De är den [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) tillägg och [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) tillägg

Vi måste omvandla VS Code till en Python IDE för att fungera med Azure Machine Learning. Arbeta med [Python i Visual Studio Code](https://code.visualstudio.com/docs/languages/python), kräver Microsoft Python-tillägget som installeras med verktyg för AI automatiskt. Tillägget gör VS Code en utmärkt IDE och fungerar på alla operativsystem med en mängd olika Python tolkar. Den använder alla VS Code förmåga att tillhandahålla automatisk komplettering och IntelliSense, ifyllningar, felsökning och Enhetstestning, samt möjlighet att enkelt växla mellan conda-miljöer och Python-miljöer, inklusive virtuella. Kolla in den här genomgången redigerar, körs, och felsökning Python-kod kan se den [Python Hello World-självstudier](https://code.visualstudio.com/docs/python/python-tutorial)

**Du installerar verktygen för AI-tillägg:**

1. Starta VS Code.

1. I en webbläsare, besök: https://aka.ms/vscodetoolsforai. 

1. I den webbsidan klickar du på **installera**. 

1. Klicka på fliken tillägg **installera**.

1. En Välkommen flik öppnas i VS Code för tillägget och Azure symbolen läggs till i Aktivitetsfältet.

   ![Azure-ikonen i Aktivitetsfältet Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. I dialogrutan klickar du på **logga In** och följ anvisningarna på skärmen för att autentisera med Azure. 
   
   Tillägget Azure Account som installerades tillsammans med VS Code Tools för AI, kan du autentisera med ditt Azure-konto. Se en lista över kommandon i den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) sidan.

> [!Tip] 
> Kolla in den [IntelliCode-tillägg för VS Code (förhandsversion)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode innehåller en uppsättning av AI-assisted funktioner för IntelliSense i Python, till exempel härledning av mest relevanta automatisk-slutförande baserat på den aktuella kontexten i kod.

## <a name="install-the-sdk"></a>Installera SDK:n

1. Se till att Python 3.5 eller högre är installerad och identifieras av VS Code. Om du installerar den nu starta om VS Code och välj en Python-tolk med hjälp av anvisningarna i https://code.visualstudio.com/docs/python/python-tutorial.

1. I VS Code, öppna kommandopaletten **Ctrl + Skift + P**.

1. Typen ”installera Azure ML SDK” att hitta pip installationskommando för SDK. En lokal privat Python-miljö skapas med Visual Studio Code förutsättningarna för att arbeta med Azure Machine Learning.
   ![Installera](./media/vscode-tools-for-ai/install-sdk.png)

1. I fönstret för den integrera terminalen, ange Python-tolk att använda eller du kan träffa **RETUR** du använder din standard Python-tolk.

   ![Installera](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Kom igång med Azure Machine Learning

Innan du börjar utbildning och distribuera maskininlärningsmodeller som använder VS Code, måste du skapa en [Azure Machine Learning-tjänstens arbetsyta](concept-azure-machine-learning-architecture.md#workspace) i molnet för att innehålla dina modeller och resurser. Lär dig hur du skapar en och skapa ditt första experiment på arbetsytan.

1. Klicka på ikonen Azure i Visual Studio Code Aktivitetsfältet. Azure: Machine Learning sidopanelen visas.

   ![Installera](./media/vscode-tools-for-ai/createworkspace.gif)

1. Högerklicka på din Azure-prenumeration och välj **Skapa arbetsyta**. En lista visas. Prenumerationens namn är ”OpenMind Studio” i animerade avbildningen, och arbetsytan som är ”MyWorkspace”. 

1. Välj en befintlig resursgrupp i listan eller skapa en ny med hjälp av guiden i kommandopaletten.

1. Ange ett unikt och rensa namn för den nya arbetsytan i fältet. I skärmbilderna heter arbetsytan ”MyWorkspace”.

1. Tryck på RETUR och den nya arbetsytan har skapats. Den visas i trädet nedan prenumerationens namn.

1. Högerklicka på namnet på arbetsytan och välj **skapa Experiment** på snabbmenyn.  Experiment Håll koll på dina körningar med hjälp av Azure Machine Learning.

1. I fältet kan du ange ett namn för experimentet. I skärmbilderna heter experimentet ”MNIST”.
 
1. Tryck på RETUR och nytt experiment skapas. Den visas i trädet nedan arbetsytans namn.

1. Högerklicka på experimentnamnet på och välj **bifoga en lokal mapp**. Den här mappen ska innehålla din lokala Python-skript. Mappen länkas sedan till arbetsytan för experimentet i molnet. 

   Nu körs var och en av ditt experiment med experimentet så att alla viktiga mått sparas i historiken experiment och modeller du tränar får automatiskt överförs till Azure Machine Learning och lagras med dina experiment mått och loggar.

   [![Bifoga en mapp i VS Code](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Använda kortkommandon

Som de flesta av VS Code är Azure Machine Learning-funktioner i VS Code tillgängliga från tangentbordet. Viktigaste tangentkombinationen veta är Ctrl + Skift + P, vilket ger dig Kommandopaletten. Härifrån kan har du åtkomst till alla funktioner i VS Code, inklusive kortkommandon för de vanligaste åtgärderna.

[![Kortkommandon för VS Code Tools för AI](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Nästa steg

Du kan nu använda Visual Studio Code för att arbeta med Azure Machine Learning.

Lär dig hur du [skapa beräkningsmål, träna och distribuera modeller i Visual Studio Code](how-to-vscode-train-deploy.md).