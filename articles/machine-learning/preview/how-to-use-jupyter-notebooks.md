---
title: "Använda Jupyter Notebooks i Azure Machine Learning-arbetsstationen | Microsoft Docs"
description: "Guide för att använda funktionen Jupyter Notebooks i Azure Machine Learning arbetsstationen"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 80cdd07bff865776a68897a7b8c1b3fe66b76b18
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Hur du använder Jupyter-anteckningsbok i Azure Machine Learning arbetsstationen

Azure Machine Learning arbetsstationen stöder interaktiva datavetenskap experiment via dess integrering av Jupyter-anteckningsbok. Den här artikeln beskriver hur du gör effektiv användning av den här funktionen för att öka hastigheten och kvaliteten på din interaktiva datavetenskap experiment.

## <a name="prerequisites"></a>Krav
- [Installera och skapa Azure Machine Learning](/machine-learning/preview/quickstart-installation.md).
- Bekanta dig med [Jupyter-anteckningsbok](http://jupyter.org/)eftersom den här artikeln inte handlar om lära så använder du Jupyter.

## <a name="jupyter-notebook-architecture"></a>Arkitektur för Jupyter-anteckningsbok
Jupyter-anteckningsbok arkitekturen med tre komponenter på en hög nivå, varje kan köras i olika beräknings-miljöer:

- **Klienten**: tar emot indata från användare och visar renderas utdata
- **Servern**: webbservern som värd för de bärbara datorer (filerna .ipynb)
- **Kernel**: körningsmiljön där faktiska körningen av cellerna anteckningsboken händer

Mer information hittar du officiellt [Jupyter dokumentationen](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Följande är ett diagram som illustrerar hur den här klienten, servern och kernel-arkitektur mappas till komponenterna i Azure ML.

![arkitektur för bärbara datorer](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Kernlar som är i Azure ML-arbetsstationen anteckningsbok
Du kan använda många olika kärnor i Azure ML-arbetsstationen genom att konfigurera kör konfigurationer och beräkna mål i den `aml_config` mappen i projektet. Lägg till en ny beräkning mål genom att utfärda `az ml computetarget attach` kommandot är detsamma som att lägga till en ny kernel.

>[!NOTE]
>Granska de [konfigurera körning](experimentation-service-configuration.md) för mer information om kör konfigurationer och beräkna mål.

### <a name="kernel-naming-convention"></a>Kernel namngivningskonvention
De kernlar som är vanligtvis har namnet i formatet ”\<projektnamn > \<kör config namn >”. Om du har en kör konfiguration med namnet till exempel _docker-python_ i ett projekt med namnet _myIris_, du kan hitta en kärna med namnet ”myIris docker-python” i listan kernel när du öppnar en Jupyter-anteckningsbok.

Arbetsstationen stöder för närvarande följande typer av kärnor.

### <a name="local-python-kernel"></a>Lokala Python-kernel
Den här Python kernel har stöd för körning på den lokala datorn. Det är integrerat med Azure Machine Learning Körningshistorik stöd. Namnet på kerneln är vanligtvis ”my_project_name lokala”.

>[!NOTE]
>Använd inte ”Python 3” kernel. Det är en fristående kernel som tillhandahålls av Jupyter som standard. Det är inte integrerat med Azure Machine Learning-funktioner.

### <a name="python-kernel-in-docker-local-or-remote"></a>Python Kernel i Docker (lokala eller fjärranslutna)
Den här Python kernel körs i en dockerbehållare på den lokala datorn eller i en fjärransluten Linux-VM. Namnet på kerneln är vanligtvis ”my_project docker”. Den associerade `docker.runconfig` filen har det `Framework` fältet inställt på `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>PySpark-kerneln i Docker (lokala eller fjärranslutna)
Den här PySpark-kerneln kör skript i ett Spark-kontexten körs i dockerbehållare på den lokala datorn eller på en fjärransluten Linux-VM. Kernel-namnet är vanligtvis ”my_project docker”. Den associerade `docker.runconfig` filen har det `Framework` fältet inställt på `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>PySpark-kerneln på HDInsight-kluster
Den här kernel körs i HDInsight-fjärrklustret bifogade som beräkning mål för projektet. Kernel-namnet är vanligtvis ”my_project my_hdi”. 

>[!IMPORTANT]
>I den `.compute` -filen för HDI compute mål, måste du ändra den `yarnDeployMode` till `client` (standardvärdet är `cluster`) för att kunna använda den här kernel. 

## <a name="start-jupyter-server-from-the-workbench"></a>Starta Jupyter Server från arbetsstationen
Från Azure Machine Learning arbetsstationen anteckningsböcker kan nås via arbetsstationen **anteckningsböcker** fliken. Den _klassificera Iris_ exempelprojektet innehåller en `iris.ipynb` exempel bärbar dator.

![fliken för bärbara datorer](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

När en bärbar dator öppnas i Azure Machine Learning arbetsstationen, visas det i sin egen dokumentfliken i **Förhandsläge**. Detta är en skrivskyddad vy som inte kräver en server som körs Jupyter och kernel.

![bärbar dator preview](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Klicka på **starta anteckningsboken Server** knappen startar Jupyter-server och växlar anteckningsboken i **redigeringsläge**. Bekant Jupyter-anteckningsbok användargränssnittet visas inbäddad på arbetsstationen. Nu kan du ange en kernel från den **Kernel** menyn och starta sessionen interaktiva bärbar dator. 

>[!NOTE]
>Observera för icke-lokala kärnor, det kan ta en minut eller två att starta om du använder den första gången. Du kan köra `az ml experiment prepare` från CLI fönster för att förbereda beräknings-målet så kernel kan starta snabbare när compute-mål förbereds.

![Redigeringsläge](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Det här är en helt interaktiv Jupyter-anteckningsbok upplevelse. Alla vanlig anteckningsbok åtgärder och kortkommandon stöds från det här fönstret med undantag för vissa åtgärder, eftersom de kan göras via arbetsstationen **anteckningsböcker** fliken och **filen** fliken.

## <a name="start-jupyter-server-from-command-line"></a>Starta Jupyter Server från kommandoraden
Du kan också starta en bärbar dator-session genom att utfärda ett `az ml notebook start` från fönstret kommandoraden:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Standardwebbläsaren startas automatiskt med Jupyter-server pekar till projektet arbetskatalogen. Du kan också använda URL och token som visas i fönstret CLI för att starta andra webbläsarfönster lokalt. 

![instrumentpanelen för projektet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Nu kan du klicka på en `.ipynb` filerna, öppna det, och kernel (om det inte har angetts), och starta interaktiva sessionen.

![instrumentpanelen för projektet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Använda magiskt kommandon för att hantera experiment

Du kan använda [magiska kommandon](http://ipython.readthedocs.io/en/stable/interactive/magics.html) i din bärbara dator celler att spåra historiken kör och spara utdata, till exempel modeller eller datauppsättningar.

Om du vill spåra enskilda anteckningsboken kör cell, Använd ”% azureml-historik i” magiskt kommandot. När du har aktiverat historiken visas varje cell körning som posten i körningshistorik.

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Om du vill aktivera cell Kör spårning kommandot ”% azureml-historik av” magiskt.

Du kan använda ”% azureml överför” magiskt kommando för att spara modellen och datafiler från din kör. Sparade objekt visas som utdata i kör historikvyn för körning.

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Utdata måste sparas till en mapp med namnet ”utdata”

## <a name="next-steps"></a>Nästa steg
- Ta reda på hur du använder Jupyter-anteckningsbok den [Jupyter officiella dokumentation](http://jupyter-notebook.readthedocs.io/en/latest/).    
- För att få en bättre förståelse av körningsmiljön för Azure ML experiment, granska [översikt av Azure Machine Learning-experiment service](experimentation-service-configuration.md)

