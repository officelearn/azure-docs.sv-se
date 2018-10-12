---
title: Konfigurera en utvecklingsmiljö för Azure Machine Learning | Microsoft Docs
description: 'Lär dig mer om att konfigurera en utvecklingsmiljö när du arbetar med Azure Machine Learning-tjänsten. I det här dokumentet lär du dig hur du använder Conda-miljöer, skapa konfigurationsfiler och konfigurera Jupyter Notebooks, Azure anteckningsböcker, IDE: er, kod redigerare och den virtuella datorn för datavetenskap.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 9938772618e9a85f7d85678c4ddde3147cbc40dd
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091869"
---
# <a name="configure-a-development-environment-for-the-azure-machine-learning-service"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning-tjänsten

Lär dig hur du konfigurerar din utvecklingsmiljö för att arbeta med Azure Machine Learning-tjänsten. Du kommer lära dig hur du skapar en konfigurationsfil som kopplar din miljö till en arbetsyta för Azure Machine Learning-tjänsten. Du får också lära dig hur du konfigurerar följande utvecklingsmiljöer:

* Jupyter Notebooks på din dator
* Visual Studio-koden
* Kod redigeringsprogram

Den rekommenderade metoden är att använda jämför Anaconda [conda-miljöer](https://conda.io/docs/user-guide/tasks/manage-environments.html) att isolera din arbetsmiljö för att undvika dependency konflikter mellan paket. Den här artikeln visar hur du ställer in en conda-miljö och använder den för Azure Machine Learning.


## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänsten. Om du vill skapa ett, Följ stegen i den [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) dokumentet.

* [Jämför Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://conda.io/miniconda.html) Pakethanteraren.

 * För Visual Studio Code miljö den [Python-tillägg](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Shell-kommandon som används i det här dokumentet har testats med bash i Linux och macOS. Kommandona är också testats med cmd.exe på Windows.

## <a name="create-workspace-configuration-file"></a>Skapa konfigurationsfil för arbetsyta

Konfigurationsfilen arbetsytan används av SDK: N för att kommunicera med din arbetsyta för Azure Machine Learning-tjänsten.  Det finns två sätt att hämta den här filen:

* Slutför den [snabbstarten](quickstart-get-started.md) att skapa en fil för arbetsytan och konfiguration. Filen `config.json` skapas åt dig i Azure-anteckningsböcker.  Den här filen innehåller konfigurationsinformation för arbetsytan.  Ladda ned eller kopiera den till samma katalog som skript eller anteckningsböcker som hänvisar till den.


* Skapa konfigurationsfilen själv med följande steg:

    1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com). Kopiera den __Arbetsytenamn__, __resursgrupp__, och __prenumerations-ID__. Dessa värden används för att skapa konfigurationsfilen.

        ![Azure Portal](./media/how-to-configure-environment/configure.png) 
    
    1. Skapa filen med den här Python-koden. Kör koden i samma katalog som skript eller anteckningsböcker som refererar till arbetsytan:

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'
        
        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        Detta skriver du följande `aml_config/config.json` fil: 
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Du kan kopiera den `aml_config` katalog eller bara `config.json` filen till en annan katalog som refererar till arbetsytan.

>[!NOTE] 
>Andra skript och anteckningsböcker i samma katalog eller under läser in arbetsytan med `ws=Workspace.from_config()`

## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Azure-anteckningsböcker och virtuell dator för datavetenskap

Azure-anteckningsböcker och Azure Data Science Virtual Machines (DSVM) är förinställd på att arbeta med Azure Machine Learning-tjänsten. Nödvändiga komponenter, bland annat Azure Machine Learning SDK, som är förinstallerade i dessa miljöer.

Azure-datorer är en Jupyter Notebook-tjänst i Azure-molnet. Den virtuella datorn för datavetenskap är en VM-avbildning som är förkonfigurerade för data science fungerar. Den virtuella datorn innehåller populära verktyg, IDE: er och paket som till exempel Jupyter Notebooks, pycharm med Tensorflow.

Du måste fortfarande en konfigurationsfil för arbetsytan att använda dessa miljöer.

Läs mer på den virtuella datorer för datavetenskap, [virtuella datorer för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) dokumentation.

Ett exempel på hur du använder Azure-datorer med Azure Machine Learning-tjänsten finns i den [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) dokumentet.

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Konfigurera Jupyter Notebooks på din egen dator

1. Öppna en kommandotolk eller shell.

2. Om du vill skapa en conda-miljö, använder du följande kommandon:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Det kan ta flera minuter att skapa miljön, eftersom Python 3.6 och andra komponenter kan behöva laddas ned.

3. Om du vill installera Azure Machine Learning-SDK med anteckningsboken tillägg, använder du följande kommando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl]
    ```

    > [!NOTE]
    > Om du får ett meddelande som `PyYAML` får inte vara avinstallerats klickar du i stället använder du följande kommando:
    > 
    > `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML` 

    Det kan ta flera minuter att installera SDK.

4. Om du vill installera paket för din machine learning-experimentering, använder du följande kommando och Ersätt `<new package>` till paketet som du vill installera:

    ```shell
    conda install <new package>
    ```

5. Om du vill installera en conda-medveten Jupyter Notebook-server och aktivera experiment widgetar (att visa information om körning), använder du följande kommandon:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Om du vill starta Jupyter-anteckningsbok, använder du följande kommando:

    ```shell
    jupyter notebook
    ```

7. Öppna en ny anteckningsbok och välj ”myenv” som din kernel. Verifiera sedan att du har Azure Machine Learning SDK är installerat genom att köra följande kommando i en anteckningsbok cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Konfigurera Visual Studio Code

1. Öppna en kommandotolk eller shell.

2. Om du vill skapa en conda-miljö, använder du följande kommandon:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Om du vill installera SDK: N för Azure Machine Learning, använder du följande kommando:
 
    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

4. Om du vill installera Visual Studio code Tools för AI finns i Visual Studio marketplace posten för [Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. Om du vill installera paket för din machine learning-experimentering, använder du följande kommando och Ersätt `<new package>` till paketet som du vill installera:

    ```shell
    conda install <new package>
    ```

6. Starta Visual Studio Code och sedan använda __CTRL-SHIFT-P__ för Windows eller __kommandot SKIFT P__ för Mac att hämta den __Kommandopaletten__. Ange *Python: Välj tolk*, och välj den conda-miljö som du skapade.

    > [!NOTE]
    > Visual Studio Code har automatiskt om conda miljöer på datorn. Mer information finns i [Visual Studio code-dokumentation](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Om du vill verifiera konfigurationen använder du Visual Studio Code för att skapa en ny Python-skriptfil med följande kod och kör den:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Konfigurera kod redigeringsprogram

Om du vill använda en anpassad Kodredigerare med Azure Machine Learning SDK måste först skapa conda-miljö enligt beskrivningen ovan. Följ sedan anvisningarna för varje redigerare som ska användas conda-miljö. Till exempel instruktioner för PyCharm finns på [ https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html ](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Nästa steg

* [Träna en modell i Azure Machine Learning med MNIST-datauppsättning](tutorial-train-models-with-aml.md)
