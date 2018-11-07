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
ms.date: 11/6/2018
ms.openlocfilehash: 8ce411e424d538a4a1f94300bfe5510658017f56
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238337"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning

Den här artikeln lär du dig hur du konfigurerar en utvecklingsmiljö med tjänsten Azure Machine Learning, inklusive:

- Så här skapar du en konfigurationsfil som kopplar din miljö till en arbetsyta för Azure Machine Learning-tjänsten.
- Så här konfigurerar du följande utvecklingsmiljöer:
  - Jupyter Notebooks på datorn
  - Visual Studio-koden
  - Anpassade Kodredigerare
- Hur du ställer in en [conda-miljön](https://conda.io/docs/user-guide/tasks/manage-environments.html) och använda den för Azure Machine Learning. Vi rekommenderar att du använder jämför Anaconda för att isolera din arbetsmiljö för att undvika dependency konflikter mellan paket.

## <a name="prerequisites"></a>Förutsättningar

- Konfigurera en arbetsyta för Azure Machine Learning-tjänsten. Följ stegen i [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).
- Installera antingen den [jämför Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://conda.io/miniconda.html) Pakethanteraren.
- Om du använder Visual Studio Code, får den [Python-tillägg](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Du kan testa shell-kommandon som visas i den här artikeln genom att använda bash (i Linux och Mac OS) eller Kommandotolken (i Windows).

## <a name="create-a-workspace-configuration-file"></a>Skapa en konfigurationsfil för arbetsyta

Azure Machine Learning SDK använder arbetsytan konfigurationsfilen för att kommunicera med din arbetsyta för Azure Machine Learning-tjänsten.

- Så här skapar konfigurationsfilen den [Snabbstart för Azure Machine Learning](quickstart-get-started.md).
  - Snabbstart-processen skapar en `config.json` fil i Azure-anteckningsböcker. Den här filen innehåller konfigurationsinformation för arbetsytan.
  - Ladda ned eller kopiera den `config.json` i samma katalog som skript eller anteckningsböcker som hänvisar till den.

- Du kan också skapa filen manuellt genom att följa dessa steg:

    1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com). Kopiera den __Arbetsytenamn__, __resursgrupp__, och __prenumerations-ID__. Dessa värden används för att skapa konfigurationsfilen.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

    1. Skapa filen med följande kod i Python och se till att köra koden i samma katalog som skript eller anteckningsböcker som refererar till arbetsytan:

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
        Koden skriver du följande `aml_config/config.json` fil:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Du kan kopiera den `aml_config` katalog eller bara `config.json` filen till en annan katalog som refererar till arbetsytan.

       > [!NOTE]
       > Läs in arbetsytan med andra skript och anteckningsböcker i samma katalog eller mindre `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure-datorer och virtuella datorer för datavetenskap

Azure-anteckningsböcker och Azure virtuella datorer för datavetenskap (Dsvm) kommer konfigurerad för att arbeta med Azure Machine Learning-tjänsten. Dessa miljöer innehåller nödvändiga komponenter, till exempel Azure Machine Learning-SDK.

### <a name="azure-notebooks"></a>Azure Notebooks

- Azure-datorer är en Jupyter Notebook-tjänst i Azure-molnet.
- Du måste fortfarande en konfigurationsfil för arbetsytan att använda dessa miljöer.

Ett exempel på hur du använder Azure-datorer med Azure Machine Learning-tjänsten finns i [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).

### <a name="data-science-virtual-machines"></a>Datavetenskap – virtuella datorer

- Den virtuella datorn för datavetenskap är en anpassad virtuell dator (VM)-avbildning som utformats för data science fungerar. Det innehåller:
  - Populära verktyg för datavetenskap
  - Integrated development Environment (IDE: er), till exempel PyCharm och RStudio
  - Paket som till exempel Jupyter Notebooks och Tensorflow

DSVM levereras med miljöer med flera Anaconda redan installerad. Öppna en kommandotolk /-gränssnittet för att använda Azure Machine Learning Python SDK utan att installera eventuella paket, och Använd någon av följande kommandon för att aktivera miljön:

* På den __Ubuntu__ DSVM, Använd det här kommandot:

    ```shell
    conda activate py36
    ```

* På den __Windows__ DSVM, Använd det här kommandot:

    ```shell
    conda activate AzureML
    ```

En gång i den här miljön kan du omedelbart importera SDK: T för Azure Machine Learning i build-verktyg som helst, utan att installera paketet.

```python
import azureml.core
print(azureml.core.VERSION)
```

Läs mer på den virtuella datorer för datavetenskap, [virtuella datorer för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Konfigurera en Jupyter-anteckningsböcker

1. Öppna en kommandotolk eller shell.

1. Skapa en conda-miljö med följande kommandon:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Det kan ta flera minuter att skapa miljön om Python 3.6 och andra komponenter måste hämtas.

1. Installera Azure Machine Learning-SDK med anteckningsboken tillägg och förberedelse av Data-SDK med hjälp av följande kommando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   Du kan se Python referensdokument för klasser och metoder i SDK: erna som följande:
   + [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk)
   + [Azure Machine Learning-SDK för dataförberedelse](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > Om du får ett meddelande om att `PyYAML` får inte vara avinstallerats klickar du i stället använder du följande kommando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Det kan ta flera minuter att installera SDK.

1. Installera paket för din machine learning-experimentering. Använd följande kommando och Ersätt `<new package>` till paketet som du vill installera:

    ```shell
    conda install <new package>
    ```

1. Installera en conda-medveten Jupyter Notebook-server och aktivera experiment widgetar (att visa information om körning). Använd följande kommandon:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Öppna Jupyter-anteckningsbok med följande kommando:

    ```shell
    jupyter notebook
    ```

1. Öppna en ny anteckningsbok, Välj ”myenv” som din kernel och verifiera sedan att du har installerat Azure Machine Learning SDK. Kör följande kommando i en anteckningsbok cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Konfigurera Visual Studio Code

1. Öppna en kommandotolk eller shell.

1. Skapa en conda-miljö med följande kommandon:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Installera Azure Machine Learning SDK och SDK för förberedelse av Data med följande kommando:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Installera Visual Studio code Tools för AI-tillägget. Se [Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Installera paket för din machine learning-experimentering. Använd följande kommando, ersätta `<new package>` med det paket du vill installera:

    ```shell
    conda install <new package>
    ```

1. Öppna Visual Studio Code och sedan använda **CTRL-SHIFT-P** (i Windows) eller **kommandot SKIFT P** (i Mac OS) att hämta den **Kommandopaletten**. Ange _Python: Välj tolk_ och välj den conda-miljö som du skapade.

   > [!NOTE]
   > Visual Studio Code har automatiskt om conda miljöer på datorn. Mer information finns i [Visual Studio code-dokumentation](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Verifiera konfigurationen genom att använda Visual Studio Code för att skapa en ny Python-skriptfil med följande kod och kör den:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Konfigurera en anpassad Kodredigerare

Du kan använda en Kodredigerare med valfri med Azure Machine Learning SDK.

1. Skapa din conda-miljö enligt beskrivningen i steg 2 av [konfigurera Visual Studio Code](#configure-visual-studio-code) ovan.
1. Följ anvisningarna för varje redigerare som ska användas conda-miljö. Exempelvis kan du följa den [PyCharm instruktioner](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Nästa steg

- [Träna en modell i Azure Machine Learning med MNIST-datauppsättning](tutorial-train-models-with-aml.md)
