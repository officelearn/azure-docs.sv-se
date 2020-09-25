---
title: 'Självstudie: kör python-skriptet Hello World'
titleSuffix: Azure Machine Learning
description: Del 2 av Azure ML kom igång-serien visar hur du skickar ett trivialt "Hello World" Python-skript till molnet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 52a9932db4fc261b8f3d740a316af3e852559a32
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320497"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Självstudie: kör "Hello World" Python-skript (del 2 av 4)

I den här självstudien får du lära dig hur du använder Azure Machine Learning python SDK för att skicka och köra ett python-"Hello World"-skript.

Den här självstudien är **del två i en själv studie serie i fyra delar** där du får lära dig grunderna i Azure Machine Learning och fullständiga jobbbaserade Machine Learning-uppgifter i Azure. Den här självstudien bygger på det arbete som du slutförde i [själv studie kursen, del 1: Konfigurera din lokala dator för Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Skapa och kör ett "Hello World" Python-skript lokalt
> * Skapa ett python-kontroll skript för att skicka "Hello World" till Azure Machine Learning
> * Förstå Azure Machine Learning koncept i kontroll skriptet
> * Skicka och kör Hello World
> * Visa dina kod utdata i molnet

## <a name="prerequisites"></a>Förutsättningar

- Slutför [installations programmet för den lokala datorn i del 1 av installations programmet](tutorial-1st-experiment-sdk-setup-local.md) om du inte redan har en Azure Machine Learning arbets yta.
- Introduktions kunskap om python-språket och Machine Learning-arbetsflöden.
- Lokal utvecklings miljö. Detta omfattar men är inte begränsat till Visual Studio Code, Jupyter eller pycharm med.
- Python (version 3.5-3.7).

## <a name="create-and-run-a-python-script-locally"></a>Skapa och köra ett Python-skript lokalt

Skapa en ny `src` under katalog som heter under `tutorial` katalogen för att lagra kod som du vill köra i ett Azure Machine Learning beräknings kluster. I under `src` katalogen skapar du `hello.py` python-skriptet:

```python
# src/hello.py
print("Hello world!")
```

Projekt katalog strukturen kommer nu att se ut så här:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Testa ditt skript lokalt

Du kan köra din kod lokalt, som har fördelen med interaktiv fel sökning av kod, genom att använda din favorit-IDE eller via en Terminal:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Skapa ett kontroll skript

Med ett *kontroll skript* kan du köra `hello.py` skriptet i molnet.  Med kontroll skriptet kan du styra hur och var din maskin inlärnings kod ska köras.  

I själv studie katalogen skapar du en ny python-fil med namnet `03-run-hello.py` och kopierar och klistrar in koden nedan i filen:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Förstå koden

Här är en beskrivning av hur kontroll skriptet fungerar:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Arbets ytan](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) ansluter till din Azure Machine Learning-arbetsyta så att du kan kommunicera med dina Azure Machine Learning-resurser.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) är ett enkelt sätt att organisera flera körningar under ett enda namn. Senare kan du se hur experiment gör det enkelt att jämföra måtten mellan dussin tals körningar.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) radbryter `hello.py` koden och skickar den till din arbets yta. Som namnet antyder kan du använda den här klassen för att _Konfigurera_ hur du vill att _skriptet_ ska _köras_ i Azure Machine Learning. Anger också vilka beräknings mål som skriptet ska köras på.  I den här koden är målet det beräknings kluster som du skapade i [installations guiden](tutorial-1st-experiment-sdk-setup-local.md)för.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Skickar ditt skript. Den här sändningen kallas för en [körning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true).  En körning kapslar in en enskild körning av koden. Använd en körning för att övervaka skript förloppet, avbilda utdata, analysera resultaten, visualisera mått med mera.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run`Objektet ger en referens till körningen av koden. Övervaka förloppet från Azure Machine Learning Studio med den URL som skrivs ut från python-skriptet.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Skicka in och kör din kod i molnet

Kör ditt kontroll skript, som i sin tur körs i det `hello.py` beräknings kluster som du skapade i [installations guiden](tutorial-1st-experiment-sdk-setup-local.md)för.

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Övervaka din kod i molnet med hjälp av Studio

Utdata kommer att innehålla en länk till Azure Machine Learning Studio som ser ut ungefär så här: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Följ länken och navigera till fliken **utdata + loggar** . Där kan du se en fil `70_driver_log.txt` så här:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

På rad 8 visas "Hello World!" utdataparametrar.

`70_driver_log.txt`Filen innehåller standard resultatet från en körning. Den här filen kan vara användbar vid fel sökning av fjärrkörning i molnet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien tog du ett enkelt "Hello World"-skript och körde det på Azure. Du har sett hur du ansluter till din Azure Machine Learning arbets yta, skapar ett experiment och skickar in din `hello.py` kod till molnet.

I nästa själv studie kurs bygger du på de här problemen genom att köra något mer intressant än `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Självstudie: Träna en modell](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Kom ihåg att [rensa dina resurser](tutorial-1st-experiment-bring-data.md#clean-up-resources) om du vill slutföra själv studie serien här och inte förloppet till nästa steg.
