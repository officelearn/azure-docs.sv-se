---
title: "Använda Jupyter Notebooks i Azure Machine Learning arbetsstationen | Microsoft Docs"
description: "En guide för att använda funktionen Jupyter Notebook i Azure Machine Learning arbetsstationen"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: c21b7096f689efedacd6e7d55d83912d35dff803
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Använda Jupyter Notebooks i Azure Machine Learning-arbetsstationen

Azure Machine Learning arbetsstationen stöder interaktiva datavetenskap experiment via dess integrering med Jupyter-anteckningsböcker. Den här artikeln beskriver hur du gör effektiv användning av den här funktionen för att öka hastigheten och kvaliteten på din interaktiva datavetenskap experiment.

## <a name="prerequisites"></a>Förutsättningar
- [Skapa konton i Azure Machine Learning och installera Azure Machine Learning arbetsstationen](quickstart-installation.md).
- Bekanta dig med de [Jupyter-anteckningsbok](http://jupyter.org/). Den här artikeln är inte om lära sig använda Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arkitektur för Jupyter-anteckningsbok
Arkitektur för Jupyter-anteckningsbok innehåller tre komponenter på en hög nivå. Varje kan köra olika beräknings-miljöer:

- **Klienten**: tar emot indata från användare och visar renderas utdata.
- **Servern**: webbservern som är värd för de bärbara datorer (filerna .ipynb).
- **Kernel**: körningsmiljö vilka körning av notebook celler händer.

Mer information finns i officiellt [Jupyter dokumentationen](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Följande diagram visar hur den här klienten, servern och kernel-arkitekturen mappar till komponenter i Azure Machine Learning:

![Arkitektur för Jupyter-anteckningsbok](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Kernlar som är i Azure Machine Learning arbetsstationen bärbara datorer
Du kan använda olika kärnor i Azure Machine Learning arbetsstationen genom att definiera kör konfigurationer och beräkna mål i den `aml_config` mappen i projektet. Lägg till en ny beräkning mål genom att utfärda de `az ml computetarget attach` kommandot är detsamma som att lägga till en ny kernel.

>[!NOTE]
>Granska [konfigurerar Azure Machine Learning experiment Service](experimentation-service-configuration.md) för mer information om kör konfigurationer och beräkna mål.

### <a name="kernel-naming-convention"></a>Kernel namngivningskonvention
Azure Machine Learning arbetsstationen genererar anpassade Jupyter kärnor. Dessa kärnor namnges  *\<projektnamn > \<kör config namn >*. Om du har en kör konfiguration med namnet till exempel _docker-python_ i ett projekt med namnet _myIris_, Azure Machine Learning tillgängliggör en kärna med namnet *myIris docker-python.* Du anger körs kernel i Jupyter-anteckningsbok **Kernel** menyn i den **ändra kernel** undermenyer. Namnet på körs kernel visas längst till höger på menyraden.
 
Azure Machine Learning arbetsstationen stöder för närvarande följande typer av kärnor.

### <a name="local-python-kernel"></a>Lokala Python-kernel
Den här Python kernel stöder körning på lokala datorer. Det är integrerat med Azure Machine Learning Körningshistorik stöd. Namnet på kerneln är vanligtvis *my_project_name lokalt.*

>[!NOTE]
>Använd inte Python 3-kernel. Det är en fristående kernel som tillhandahålls av Jupyter som standard och inte är integrerad med Azure Machine Learning-funktioner. Till exempel den `%azureml` Jupyter magiskt funktioner returnerar ”hittades inte” fel. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python kernel i Docker (lokala eller fjärranslutna)
Den här Python kernel körs i en dockerbehållare på den lokala datorn eller i en fjärransluten Linux-dator (VM). Namnet på kerneln är vanligtvis *my_project docker.* Den associerade `docker.runconfig` filen har det `Framework` fältet inställt på `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>PySpark-kerneln i Docker (lokala eller fjärranslutna)
Den här PySpark-kerneln kör skript i en kontext för Spark som körs i en dockerbehållare på den lokala datorn eller på en fjärransluten Linux-VM. Kernel-namnet är vanligtvis *my_project docker.* Den associerade `docker.runconfig` filen har det `Framework` fältet inställt på `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>PySpark-kerneln i Azure HDInsight-kluster
Den här kernel körs i Azure HDInsight fjärrklustret bifogade som beräkning mål för projektet. Kernel-namnet är vanligtvis *my_project my_hdi.* 

>[!IMPORTANT]
>I den `.compute` -filen för HDI compute mål, måste du ändra den `yarnDeployMode` till `client` (standardvärdet är `cluster`) att använda den här kernel. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Starta en Jupyter-server från Azure Machine Learning arbetsstationen
Från Azure Machine Learning arbetsstationen du har åtkomst till bärbara datorer via den **anteckningsböcker** fliken. Den _klassificera Iris_ exempelprojektet innehåller en `iris.ipynb` exempel bärbar dator.

![fliken för bärbara datorer](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

När du öppnar en bärbar dator i Azure Machine Learning arbetsstationen visas det i sin egen dokumentfliken i **Förhandsläge**. Detta är en skrivskyddad vy som inte kräver en server som körs Jupyter och kernel.

![bärbar dator preview](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Att välja den **starta anteckningsboken Server** Jupyter-server startas och växlar anteckningsboken i **redigeringsläge**. Bekant Jupyter-anteckningsbok användargränssnittet visas inbäddad på arbetsstationen. Nu kan du ange en kernel från den **Kernel** menyn och starta sessionen interaktiva bärbar dator. 

>[!NOTE]
>Det kan ta en minut eller två att starta om du använder den första gången med icke-lokala kärnor. Du kan köra den `az ml experiment prepare` från fönstret CLI för att förbereda beräknings-målet så kernel startas mycket snabbare när compute-mål förbereds.

![Redigeringsläge](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Det här är en helt interaktiv Jupyter Notebook-upplevelse. Alla vanlig anteckningsbok åtgärder och kortkommandon stöds från det här fönstret, utom vissa åtgärder som kan göras via arbetsstationen **anteckningsböcker** fliken och **filen** fliken.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Starta en Jupyter-server från kommandoraden
Du kan också starta en bärbar dator-session genom att utfärda `az ml notebook start` från fönstret kommandoraden:
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
Din standardwebbläsare öppnas automatiskt med Jupyter-servern pekar till projektet arbetskatalogen. Du kan också använda URL och token som visas i fönstret CLI för att öppna andra webbläsarfönster lokalt. 

![instrumentpanelen för projektet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Nu kan du välja en `.ipynb` filerna, öppna den, ange kernel (om det inte har angetts) och starta interaktiva sessionen.

![instrumentpanelen för projektet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Använda magiskt kommandon för att hantera experiment

Du kan använda [magiska kommandon](http://ipython.readthedocs.io/en/stable/interactive/magics.html) i din bärbara dator celler att spåra historiken kör och spara utdata, till exempel modeller eller datauppsättningar.

Om du vill spåra enskilda anteckningsboken cell körs använder de `%azureml history on` magiska kommando. När du har aktiverat historiken visas varje cell körning som en post i körningshistoriken:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Stäng av cell Kör spårning genom att använda den `%azureml history off` magiska kommando.

Du kan använda den `%azureml upload` magiska kommando för att spara modellen och datafiler från din kör. Sparade objekt visas som utdata i vyn körningshistorik:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Utdata måste sparas till en mapp med namnet *matar ut.*

## <a name="next-steps"></a>Nästa steg
- Information om hur du använder Jupyter-anteckningsbok finns i [Jupyter officiella dokumentation](http://jupyter-notebook.readthedocs.io/en/latest/).    
- För att få en bättre förståelse av körningsmiljön för Azure Machine Learning-experiment, se [konfigurerar Azure Machine Learning experiment Service](experimentation-service-configuration.md).

