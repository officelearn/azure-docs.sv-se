---
title: 'Självstudiekurs: Konfigurera tillägget Visual Studio-kod'
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar Tillägget Visual Studio Code Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272938"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Konfigurera Azure Machine Learning Visual Studio-kodtillägg

Lär dig hur du installerar och kör skript med azure machine learning Visual Studio-kodtillägget.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Installera azure Machine Learning Visual Studio-kodtillägget
> * Logga in på ditt Azure-konto från Visual Studio-kod
> * Använda Azure Machine Learning-tillägget för att köra ett exempelskript

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har någon, registrera dig för att prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Om du inte har det, [installera det](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3 (Svenska)](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Installera tillägget

1. Öppna Visual Studio Code.
1. Välj Tilläggsikonen i **aktivitetsfältet** för att öppna tilläggsvyn. **Activity Bar**
1. Sök efter "Azure Machine Learning" i vyn Tillägg.
1. Välj **Installera**.

    > [!div class="mx-imgBorder"]
    > ![Installera Azure Machine Learning VS-kodtillägg](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Du kan också installera Azure Machine Learning-tillägget via Visual Studio Marketplace genom [att hämta installationsprogrammet direkt](https://aka.ms/vscodetoolsforai). 

Resten av stegen i den här självstudien har testats med **version 0.6.8** av tillägget.

## <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

För att kunna etablera resurser och köra arbetsbelastningar på Azure måste du logga in med dina Azure-kontouppgifter. För att hjälpa till med kontohantering installerar Azure Machine Learning automatiskt Azure Account-tillägget. Besök följande webbplats om du vill [veta mer om Tillägget för Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Öppna kommandopaletten genom att välja **Visa > kommandopalett** på menyraden. 
1. Ange kommandot "Azure: Sign in" i kommandopaletten för att starta inloggningsprocessen.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Kör ett utbildningsskript för maskininlärningsmodell i Azure

Nu när du har loggat in på Azure med dina kontouppgifter, Följ stegen i det här avsnittet för att lära dig hur du använder tillägget för att träna en maskininlärningsmodell.

1. Ladda ned och packa upp [VS-kodverktygen för AI-databasen](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) var som helst på datorn.
1. Öppna `mnist-vscode-docs-sample` katalogen i Visual Studio-kod.
1. Välj **Azure-ikonen** i aktivitetsfältet.
1. Välj ikonen **Kör experiment** högst upp i Azure Machine Learning View.

    > [!div class="mx-imgBorder"]
    > ![Kör experiment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. När kommandopaletten expanderar följer du anvisningarna.

    1. Välj din Azure-prenumeration.
    1. Välj **Conda dependencies-fil**i listan över miljöer .
    1. Tryck **på Retur** för att bläddra i conda-beroendefilen. Den här filen innehåller de beroenden som krävs för att köra skriptet. I det här fallet är `env.yml` beroendefilen `mnist-vscode-docs-sample` filen i katalogen.
    1. Tryck på **Retur** för att bläddra i skriptfilen för utbildning. Det här är filen som innehåller kod till en maskininlärningsmodell som kategoriserar bilder av handskrivna siffror. I det här fallet är skriptet `train.py` för `mnist-vscode-docs-sample` att träna modellen filen i katalogen.

1. Nu visas en konfigurationsfil som liknar den nedan i textredigeraren. Konfigurationen innehåller den information som krävs för att köra utbildningsjobbet som filen som innehåller koden för att träna modellen och eventuella Python-beroenden som angetts i föregående steg.

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
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

1. När du är nöjd med konfigurationen skickar du in experimentet genom att öppna kommandopaletten och ange följande kommando:

    ```text
    Azure ML: Submit Experiment
    ```

    Detta skickar `train.py` och konfigurationsfilen till din Azure Machine Learning-arbetsyta. Utbildningsjobbet startas sedan på en beräkningsresurs i Azure.

### <a name="track-the-progress-of-the-training-script"></a>Spåra förloppet för utbildningsskriptet

Det kan ta flera minuter att köra skriptet. Så här följer du dess framsteg:

1. Välj **Azure-ikonen** i aktivitetsfältet.
1. Expandera prenumerationsnoden.
1. Expandera noden experiment som körs för tillfället. Detta finns i `{workspace}/Experiments/{experiment}` noden där värdena för arbetsytan och experimentet är desamma som de egenskaper som definierats i konfigurationsfilen.
1. Alla körningar för experimentet listas, liksom deras status. Om du vill få den senaste statusen klickar du på uppdateringsikonen högst upp i Azure Machine Learning View.

    > [!div class="mx-imgBorder"]
    > ![Spåra experimentframsteg](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Ladda ner den tränade modellen

När experimentkörningen är klar är utdata en tränad modell. Så här hämtar du utdata lokalt:

1. Högerklicka på den senaste körningen och välj **Hämta utdata**.

    > [!div class="mx-imgBorder"]
    > ![Ladda ner utbildad modell](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Välj en plats där utgångarna ska sparas.
1. En mapp med namnet på din körning hämtas lokalt. Navigera till den.
1. Modellfilerna finns `outputs/outputs/model` i katalogen.

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Träna och distribuera en avbildningsklassificering TensorFlow-modell med azure machine learning visual studio-kodtillägg](tutorial-train-deploy-image-classification-model-vscode.md).
