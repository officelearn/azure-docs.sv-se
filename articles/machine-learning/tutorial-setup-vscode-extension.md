---
title: 'Självstudie: Konfigurera Visual Studio Code Extension (för hands version)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du installerar och kör utbildnings skript med Azure Machine Learning Visual Studio Code Extension.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 11/16/2020
ms.openlocfilehash: f485c12bf82dde798a77175a3fab4d76488150c3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701209"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension-preview"></a>Konfigurera Azure Machine Learning Visual Studio Code Extension (för hands version)

Lär dig hur du installerar och kör skript med hjälp av tillägget Azure Machine Learning Visual Studio Code.

I den här självstudien går du igenom följande aktiviteter:

> [!div class="checklist"]
> * Installera Azure Machine Learning Visual Studio Code-tillägget
> * Logga in på ditt Azure-konto från Visual Studio Code
> * Använd Azure Machine Learning-tillägget för att köra ett exempel skript

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett kan du registrera dig och prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Om du inte har det kan du [installera det](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Installera tillägget

1. Öppna Visual Studio Code.
1. Öppna vyn tillägg genom att välja **tilläggs** ikonen från **aktivitets fältet** .
1. I vyn tillägg söker du efter "Azure Machine Learning".
1. Välj **Installera**.

    > [!div class="mx-imgBorder"]
    > ![Installera Azure Machine Learning VS Code-tillägg](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Alternativt kan du installera tillägget Azure Machine Learning via Visual Studio Marketplace genom [att hämta installations programmet direkt](https://aka.ms/vscodetoolsforai). 

Resten av stegen i den här självstudien har testats med **version 0.6.8** av tillägget.

## <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

För att kunna etablera resurser och köra arbets belastningar på Azure måste du logga in med dina autentiseringsuppgifter för Azure-kontot. För att hjälpa till med konto hantering installerar Azure Machine Learning automatiskt tillägget för Azure-kontot. Besök följande webbplats om du vill [veta mer om tillägget för Azure-kontot](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Öppna paletten kommando genom att välja **visa > kommando paletten** på Meny raden. 
1. Starta inloggnings processen genom att ange kommandot "Azure: Sign in" i kommando paletten.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Köra ett utbildnings skript för Machine Learning-modellen i Azure

Nu när du har loggat in på Azure med autentiseringsuppgifterna för ditt konto kan du använda stegen i det här avsnittet för att lära dig hur du använder tillägget för att träna en maskin inlärnings modell.

1. Hämta och packa upp [vs Code-verktygen för AI-lagringsplats](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) var som helst på datorn.
1. Öppna `mnist-vscode-docs-sample` katalogen i Visual Studio Code.
1. Välj **Azure** -ikonen i aktivitets fältet.
1. Välj **Kör experiment** -ikonen överst i Azure Machine learnings visningen.

    > [!div class="mx-imgBorder"]
    > ![Kör experiment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. När paletten expanderas följer du anvisningarna.

    > [!NOTE]
    > Om du redan har installerat befintliga Azure Machine Learning-resurser kan du läsa [så här kör du experiment i vs Code-guiden](./how-to-manage-resources-vscode.md#run-experiment).

    1. Välj din Azure-prenumeration.
    1. I listan över miljöer väljer du **Conda-beroenden**.
    1. Tryck på **RETUR** för att bläddra i filen med Conda-beroenden. Den här filen innehåller de beroenden som krävs för att köra skriptet. I det här fallet är beroende filen `env.yml` filen i `mnist-vscode-docs-sample` katalogen.
    1. Tryck på **RETUR** för att bläddra i övnings skript filen. Det här är den fil som innehåller kod för en maskin inlärnings modell som kategoriserar bilder med handskrivna siffror. I det här fallet är skriptet för att träna modellen en `train.py` fil inuti `mnist-vscode-docs-sample` katalogen.

1. I det här läget visas en konfigurations fil som liknar den som visas nedan i text redigeraren. Konfigurationen innehåller den information som krävs för att köra utbildnings jobbet, t. ex. filen som innehåller koden för att träna modellen och eventuella python-beroenden som anges i föregående steg.

    ```json
    {
        "workspace": "WS06271500",
        "resourceGroup": "WS06271500-rg2",
        "location": "South Central US",
        "experiment": "WS06271500-exp1",
        "compute": {
            "name": "WS06271500-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS06271500-com1-rc1",
            "environment": {
                "name": "WS06271500-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. När du är nöjd med konfigurationen skickar du experimentet genom att öppna kommando-paletten och ange följande kommando:

    ```text
    Azure ML: Submit Experiment
    ```

    Detta skickar- `train.py` och-konfigurations filen till din Azure Machine Learning-arbetsyta. Utbildnings jobbet startas sedan på en beräknings resurs i Azure.

### <a name="track-the-progress-of-the-training-script"></a>Följa förloppet för övnings skriptet

Det kan ta flera minuter att köra skriptet. Så här spårar du förloppet:

1. Välj **Azure** -ikonen i aktivitets fältet.
1. Expandera noden prenumeration.
1. Expandera den pågående experimentets nod. Detta finns inuti `{workspace}/Experiments/{experiment}` noden där värdena för din arbets yta och experiment är desamma som de egenskaper som definierats i konfigurations filen.
1. Alla körningar för experimentet visas i listan, samt deras status. Om du vill hämta den senaste statusen klickar du på ikonen uppdatera överst i vyn Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Spåra experiment förlopp](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Ladda ned den tränade modellen

När experiment körningen är klar är utdata en utbildad modell. Så här hämtar du utdata lokalt:

1. Högerklicka på den senaste körningen och välj **Hämta utdata**.

    > [!div class="mx-imgBorder"]
    > ![Hämta utbildad modell](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Välj en plats där du vill spara utdata.
1. En mapp med namnet på din körning laddas ned lokalt. Navigera till den.
1. Model-filerna finns i `outputs/outputs/model` katalogen.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: träna och distribuera en bild klassificering TensorFlow modell med Azure Machine Learning Visual Studio Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md).
