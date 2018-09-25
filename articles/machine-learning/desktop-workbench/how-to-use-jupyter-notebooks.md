---
title: Så här använder du Jupyter-anteckningsböcker i Azure Machine Learning Workbench | Microsoft Docs
description: En guide för att använda funktionen Jupyter Notebook i Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c59969d5a56bd2eb4eb8c490d4f1b5af20d7f056
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998703"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Använd Jupyter-anteckningsböcker i Azure Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Azure Machine Learning Workbench stöder interaktiva data science experimentering via dess integrering med Jupyter-anteckningsböcker. Den här artikeln beskriver hur du gör effektiv användning av den här funktionen för att öka frekvensen och kvaliteten på dina interaktiva data science experimentering.

## <a name="prerequisites"></a>Förutsättningar
- [Skapa Azure Machine Learning-konton och installera Azure Machine Learning Workbench](quickstart-installation.md).
- Känna till de [Jupyter Notebook](http://jupyter.org/). Den här artikeln är inte om hur du använder Jupyter.

## <a name="jupyter-notebook-architecture"></a>Jupyter Notebook-arkitektur
På hög nivå innehåller Jupyter Notebook arkitektur tre komponenter. Var och en kan köra i olika beräkningsmiljöer:

- **Klienten**: tar emot användarindata och visar renderade utdata.
- **Server**: webbservern som är värd för notebook-filer (.ipynb-filer).
- **Kernel**: Runtime-miljö i vilken körningen av anteckningsboken celler händer.

Mer information finns i officiellt [Jupyter dokumentation](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Följande diagram visar hur den här arkitekturen för klienten och servern kernel mappar till komponenter i Azure Machine Learning:

![Jupyter Notebook-arkitektur](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Kernlar som är i Azure Machine Learning Workbench anteckningsböcker
Du kan använda olika kärnor i Azure Machine Learning Workbench genom att definiera kör konfigurationer och beräkningsmål i den `aml_config` mappen i projektet. Att lägga till en ny beräkningsmål genom att utfärda det `az ml computetarget attach` kommandot är detsamma som att lägga till en ny kernel.

>[!NOTE]
>Granska [konfigurerar Azure Machine Learning Experimentation Service](experimentation-service-configuration.md) för mer information om kör konfigurationer och beräkningsmål.

### <a name="kernel-naming-convention"></a>Namngivningskonvention för kernel
Azure Machine Learning Workbench skapar anpassade kernlar i Jupyter. Dessa kernlar som är namngivna  *\<projektnamn > \<kör config namn >*. Exempel: Om du har en kör konfiguration med namnet _docker-python_ i ett projekt med namnet _myIris_, Azure Machine Learning tillgängliggör en kernel med namnet *myIris docker-python.* Du anger i kernel som körs i Jupyter-anteckningsboken **Kernel** menyn i den **ändra kernel** undermenyn. Namnet på kerneln som körs visas längst till höger på menyraden.
 
Azure Machine Learning Workbench stöder för närvarande följande typer av kernel.

### <a name="local-python-kernel"></a>Lokal Python-kernel
Den här Python-kernel har stöd för körning på lokala datorer. Det är integrerat med stöd för Azure Machine Learning-Körningshistorik. Namnet på kerneln är vanligtvis *my_project_name lokal.*

>[!NOTE]
>Använd inte Python 3-kernel. Det är en fristående kernel som tillhandahålls av Jupyter som standard och inte är integrerad med Azure Machine Learning-funktioner. Till exempel den `%azureml` Jupyter magic funktioner returnerar ”hittades inte” fel. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python-kerneln i Docker (lokala eller fjärranslutna)
Den här Python-kernel som körs i en Docker-behållare på den lokala datorn eller i en fjärransluten Linux-dator (VM). Namnet på kerneln är vanligtvis *my_project docker.* Den associerade `docker.runconfig` filen har den `Framework` fältet inställt på `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>PySpark-kerneln i Docker (lokala eller fjärranslutna)
Den här PySpark-kerneln kör skript i en Spark-kontext som körs i en Docker-behållare, antingen på den lokala datorn eller på en fjärransluten Linux VM. Kernel-namnet är vanligtvis *my_project docker.* Den associerade `docker.runconfig` filen har den `Framework` fältet inställt på `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>PySpark-kerneln i ett Azure HDInsight-kluster
Den här kernel som körs i Azure HDInsight-fjärrklustret bifogade som beräkningsmål för ditt projekt. Kernel-namnet är vanligtvis *my_project my_hdi.* 

>[!IMPORTANT]
>I den `.compute` -filen för HDI beräkningsmålet, måste du ändra den `yarnDeployMode` fältet `client` (standardvärdet är `cluster`) att använda den här kernel. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Starta en Jupyter-server från Azure Machine Learning Workbench
Från Azure Machine Learning Workbench, kan du komma åt anteckningsböcker via den **anteckningsböcker** fliken. Den _klassificera Iris_ exempelprojektet innehåller en `iris.ipynb` exempel anteckningsboken.

![Fliken anteckningsböcker](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

När du öppnar en anteckningsbok i Azure Machine Learning Workbench, den visas i sin egen dokument-flik i **förhandsgranskningsläge**. Det här är en skrivskyddad vy som inte kräver en Jupyter-server som körs och kernel.

![Notebook-förhandsversion](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Att välja den **starta Anteckningsboksserver** knappen startar Jupyter-servern och växlar anteckningsboken i **redigeringsläge**. Jupyter Notebook välbekanta gränssnittet visas inbäddad i Workbench. Du kan nu ställa in en kernel från den **Kernel** menyn och starta interaktiv notebook-session. 

>[!NOTE]
>Det kan ta en minut eller två att starta om du använder för första gången med icke-lokala kärnor. Du kan köra den `az ml experiment prepare` från CLI-fönstret för att förbereda beräkningsmål så att kernel startas mycket snabbare när beräkningsmål förbereds.

![Redigeringsläge](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Det här är en helt interaktiva Jupyter-anteckningsbok. Alla åtgärder för vanliga anteckningsboken och kortkommandon stöds från det här fönstret, förutom några filåtgärder som kan göras via Workbench **anteckningsböcker** fliken och **filen** fliken.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Starta en Jupyter-server från kommandoraden
Du kan också starta en notebook-session genom att utfärda `az ml notebook start` från Kommandotolken:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Din standardwebbläsare öppnas automatiskt med Jupyter-servern som pekar till arbetskatalogen för projektet. Du kan också använda URL: en och token som visas i CLI-fönstret för att öppna andra webbläsarfönster lokalt. 

![Instrumentpanelen för projektet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Nu kan du välja en `.ipynb` notebook-filen, öppna den, ange kernel (om det inte har angetts) och starta interaktiva sessionen.

![Instrumentpanelen för projektet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Använda magic kommandon för att hantera experiment

Du kan använda [magic kommandon](http://ipython.readthedocs.io/en/stable/interactive/magics.html) inom din notebook celler som ska spåra ditt körningshistorik och spara utdata till exempel modeller eller datauppsättningar.

För att spåra enskilda notebook cell körs måste du använda den `%azureml history on` magic kommando. När du har aktiverat historiken visas varje cell-körning som en post i körningshistoriken:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Stäng av cell Kör spårning genom att använda den `%azureml history off` magic kommando.

Du kan använda den `%azureml upload` magic kommando för att spara modellen och datafiler från din körning. Sparade objekt visas som utdata i vyn körningshistoriken:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Utdata måste sparas till en mapp med namnet *matar ut.*

## <a name="next-steps"></a>Nästa steg
- Läs hur du använder Jupyter Notebook i den [Jupyter officiella dokumentationen](http://jupyter-notebook.readthedocs.io/en/latest/).    
- För att få en djupare förståelse för körningsmiljö för Azure Machine Learning-experimentering, se [konfigurerar Azure Machine Learning Experimentation Service](experimentation-service-configuration.md).

