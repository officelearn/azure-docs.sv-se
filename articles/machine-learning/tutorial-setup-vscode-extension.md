---
title: 'Självstudie: Konfigurera Azure Machine Learning Visual Studio Code-tillägg'
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar Visual Studio Code Azure Machine Learning-tillägget.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157614"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Konfigurera Azure Machine Learning Visual Studio Code-tillägg

Lär dig hur du installerar och kör skript med hjälp av tillägget Azure Machine Learning Visual Studio Code.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Installera Azure Machine Learning Visual Studio Code-tillägget
> * Logga in på ditt Azure-konto från Visual Studio Code
> * Använd Azure Machine Learning-tillägget för att köra ett exempel skript

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har ett kan du registrera dig och prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
- Installera [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), en lätt, plattforms oberoende kod redigerare. 

## <a name="install-the-extension"></a>Installera tillägget

1. Öppna Visual Studio Code.
1. Öppna vyn tillägg genom att välja **tilläggs** ikonen från **aktivitets fältet** .
1. I vyn tillägg söker du efter "Azure Machine Learning".
1. Välj **Installera**.

> [!NOTE]
> Alternativt kan du installera tillägget Azure Machine Learning via Visual Studio Marketplace genom [att hämta installations programmet direkt](https://aka.ms/vscodetoolsforai). 

Resten av stegen i den här självstudien har testats med **version 0.6.8** av tillägget.

## <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

För att kunna etablera resurser och köra arbets belastningar på Azure måste du logga in med dina autentiseringsuppgifter för Azure-kontot. För att hjälpa till med konto hantering installerar Azure Machine Learning automatiskt tillägget för Azure-kontot. Besök följande webbplats om du vill [veta mer om tillägget för Azure-kontot](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Öppna paletten kommando genom att välja **visa > kommando paletten** på Meny raden. 
1. Ange kommandot "Azure: Sign in" i text rutan för att starta inloggnings processen.

## <a name="run-a-script-in-azure"></a>Köra ett skript i Azure

Nu när du har loggat in på Azure med autentiseringsuppgifterna för ditt konto kan du använda stegen i det här avsnittet för att lära dig hur du använder tillägget för att träna en maskin inlärnings modell.

1. Hämta och packa upp [vs Code-verktygen för AI-lagringsplats](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) var som helst på datorn.
1. Öppna `mnist-vscode-docs-sample` Directory i Visual Studio Code.
1. Välj **Azure** -ikonen i aktivitets fältet.
1. Välj **Kör experiment** -ikonen överst i Azure Machine learnings visningen.

    > [!div class="mx-imgBorder"]
    > ![köra experiment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. När paletten expanderas följer du anvisningarna.

    1. Välj din Azure-prenumeration.
    1. Välj **skapa en ny Azure ml-arbetsyta**
    1. Välj jobb typen **TensorFlow för enskild nod** .
    1. Ange `train.py` som det skript som ska tränas. Det här är den fil som innehåller kod för en maskin inlärnings modell som kategoriserar bilder med handskrivna siffror.
    1. Ange följande paket som krav för att köra.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. I det här läget visas en konfigurations fil som liknar den som visas nedan i text redigeraren. Konfigurationen innehåller den information som krävs för att köra utbildnings jobbet, t. ex. filen som innehåller koden för att träna modellen och eventuella python-beroenden som anges i föregående steg.

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Välj **Skicka experiment** för att köra experimentet i Azure. Detta skickar `train.py`-och konfigurations filen till Azure Machine Learning-arbetsytan. Utbildnings jobbet startas sedan på en beräknings resurs i Azure.
1. Efter flera minuter skapas en katalog med namnet `output` lokalt som innehåller en utbildad TensorFlow-modell.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: träna och distribuera en bild klassificering TensorFlow modell med Azure Machine Learning Visual Studio Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md).
