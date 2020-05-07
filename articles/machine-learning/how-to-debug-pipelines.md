---
title: Felsöka & Felsök ML-pipelines
titleSuffix: Azure Machine Learning
description: Felsöka Azure Machine Learning pipelines i python. Lär dig vanliga fall GRO par för utveckling av pipelines och tips som hjälper dig att felsöka skript före och under fjärrkörning. Lär dig hur du använder Visual Studio Code för att interaktivt felsöka dina pipelines i Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 4f0eb6aa92dd8999baed6868a159c86d5e7bd0c8
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594665"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Felsöka pipelines för maskininlärning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att felsöka och felsöka [maskin inlärnings pipeliner](concept-ml-pipelines.md) i [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) och [Azure Machine Learning designer (för hands version)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Information finns i How to:

* Felsöka med hjälp av Azure Machine Learning SDK
* Felsöka med hjälp av Azure Machine Learning designer
* Felsöka med hjälp av Application Insights
* Felsöka interaktivt med Visual Studio Code (VS Code) och Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Felsöka och Felsök i Azure Machine Learning SDK
I följande avsnitt får du en översikt över vanliga fall GRO par när du skapar pipelines och olika strategier för fel sökning av kod som körs i en pipeline. Använd följande tips när du har problem med att få en pipeline att köras som förväntat.

### <a name="testing-scripts-locally"></a>Testa skript lokalt

Ett av de vanligaste felen i en pipeline är att ett kopplat skript (data rengörings skript, bedömnings skript osv.) inte körs som avsett eller innehåller körnings fel i fjärrberäknings kontexten som är svåra att felsöka i arbets ytan i Azure Machine Learning Studio. 

Det går inte att köra pipeliner lokalt, men om du kör skripten i isolering på din lokala dator kan du felsöka snabbare eftersom du inte behöver vänta på bearbetningen av beräknings-och miljö versionen. Det krävs en del utvecklings arbete för att göra detta:

* Om dina data finns i ett moln data lager måste du hämta data och göra dem tillgängliga för ditt skript. Att använda ett litet exempel på dina data är ett bra sätt att skära ned i körningen och snabbt få feedback om skript beteende
* Om du försöker simulera ett mellanliggande pipeline-steg kan du behöva bygga de objekt typer som det aktuella skriptet förväntar sig i föregående steg
* Du måste också definiera en egen miljö och replikera de beroenden som definierats i fjärrberäknings miljön

När du har en skript konfiguration som ska köras i din lokala miljö är det mycket enklare att utföra fel sökning av uppgifter, t. ex.:

* Bifoga en anpassad fel söknings konfiguration
* Pausa körning och inspektera objekt tillstånd
* Fångst typ eller logiska fel som inte ska exponeras förrän runtime

> [!TIP] 
> När du kan kontrol lera att skriptet körs som förväntat kör du ett lämpligt nästa steg i en pipeline för ett enda steg innan du försöker köra det i en pipeline med flera steg.

### <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Att testa skript lokalt är ett bra sätt att felsöka större kodfragment och komplex logik innan du börjar skapa en pipeline, men ibland behöver du felsöka skript under själva pipeline-körningen, särskilt när du diagnostiserar beteende som inträffar under interaktionen mellan pipeline-steg. Vi rekommenderar att du använder `print()` instruktioner i dina steg skript så att du kan se objekt status och förväntade värden under fjärrkörning, på samma sätt som du skulle felsöka JavaScript-kod.

Logg filen `70_driver_log.txt` innehåller: 

* Alla uttryckta instruktioner under körningen av skriptet
* Stack spårning för skriptet 

Om du vill hitta den här och andra loggfiler i portalen börjar du med att klicka på pipeline-körningen i din arbets yta.

![Sidan körnings lista för pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Gå till sidan körnings information för pipeline.

![Sidan körnings information för pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klicka på modulen för det aktuella steget. Gå till fliken **loggar** . Andra loggar innehåller information om bygg processen för miljö avbildning och steg förberedelse skript.

![Fliken logg för körning av informations sida](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Körningar för *publicerade pipelines* finns på fliken **slut punkter** i din arbets yta. Körningar för *icke-publicerade pipelines* kan hittas i **experiment** eller **pipeliner**.

### <a name="troubleshooting-tips"></a>Felsökningstips

Följande tabell innehåller vanliga problem under utveckling av pipeline, med möjliga lösningar.

| Problem | Möjlig lösning |
|--|--|
| Det gick inte att skicka `PipelineData` data till katalogen | Se till att du har skapat en katalog i skriptet som motsvarar var din pipeline förväntar dig utdata från steget. I de flesta fall definierar ett indataargument utdata-katalogen och du skapar katalogen explicit. Använd `os.makedirs(args.output_dir, exist_ok=True)` för att skapa utdatakatalogen. Se [självstudien](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) för ett bedömnings skript exempel som visar det här design mönstret. |
| Beroende buggar | Om du har utvecklat och testat skript lokalt men hittar beroende problem när du kör på en fjärrberäkning i pipelinen bör du se till att dina beräknings miljö beroenden och versioner matchar din test miljö. (Se [miljö utveckling, cachelagring och åter användning](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Tvetydiga fel med beräknings mål | Att ta bort och återskapa beräknings mål kan lösa vissa problem med beräknings mål. |
| Pipeline återanvändar inte steg | Steg åter användning är aktiverat som standard, men se till att du inte har inaktiverat det i ett steg i pipeline. Om åter användning är inaktive rad kommer `allow_reuse` parametern i steget att ställas in på. `False` |
| Pipelinen körs inte nödvändigt vis | För att se till att stegen bara körs igen när deras underliggande data eller skript ändras, kan du koppla ihop dina kataloger för varje steg. Om du använder samma käll katalog för flera steg kan du få onödig omkörning. Använd `source_directory` parametern i ett pipeline-steg-objekt för att peka på den isolerade katalogen för det steget och se till att du inte `source_directory` använder samma sökväg för flera steg. |

### <a name="logging-options-and-behavior"></a>Loggnings alternativ och beteende

Tabellen nedan innehåller information om olika fel söknings alternativ för pipelines. Det är inte en fullständig lista, som andra alternativ finns förutom bara de Azure Machine Learning-, python-och Open-räkningar som visas här.

| Bibliotek                    | Typ   | Exempel                                                          | Mål                                  | Resurser                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Mått | `run.log(name, val)`                                             | Azure Machine Learning portalens användar gränssnitt             | [Spåra experiment](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml. Core. Run-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python-utskrift/-loggning    | Logga    | `print(val)`<br>`logging.info(message)`                          | Driv rutins loggar, Azure Machine Learning designer | [Spåra experiment](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python-loggning](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Python-räkningar          | Logga    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-spår                | [Felsöka pipelines i Application Insights.](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportörer](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook för python-loggning](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exempel på loggnings alternativ

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Felsöka och Felsök i Azure Machine Learning designer (för hands version)

Det här avsnittet innehåller en översikt över hur du felsöker pipelines i designern. För pipeliner som skapats i designern kan du hitta **70_driver_log** -filen på antingen sidan redigering eller på sidan körnings information för pipelinen.

### <a name="get-logs-from-the-authoring-page"></a>Hämta loggar från sidan redigering

När du skickar en pipeline-körning och stannar på sidan redigering kan du hitta de loggfiler som genereras för varje modul när varje modul har slutförts.

1. Välj en modul som har körts klart på redigerings arbets ytan.
1. I den högra rutan i modulen går du till fliken **utdata + loggar** .
1. Expandera den högra rutan och välj **70_driver_log. txt** för att visa filen i webbläsaren. Du kan också hämta loggar lokalt.

    ![Fönster för utökad utdata i designern](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Hämta loggar från pipeline-körningar

Du kan också hitta loggfilerna för vissa körningar på sidan körnings information för pipeline, som du hittar i avsnittet **pipelines** eller **experiment** i Studio.

1. Välj en pipeline-körning som skapats i designern.

    ![Sidan pipeline-körning](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Välj en modul i förhands gransknings fönstret.
1. I den högra rutan i modulen går du till fliken **utdata + loggar** .
1. Expandera den högra rutan om du vill visa filen **70_driver_log. txt** i webbläsaren eller välj filen för att ladda ned loggarna lokalt.

> [!IMPORTANT]
> Om du vill uppdatera en pipeline från sidan körnings information för pipelinen måste du **klona** pipeline-körningen till ett nytt pipeline-utkast. En pipeline-körning är en ögonblicks bild av pipelinen. Det liknar en loggfil och kan inte ändras. 

## <a name="debug-and-troubleshoot-in-application-insights"></a>Felsöka och Felsök i Application Insights
Mer information om hur du använder python-biblioteket för openräkning på det här sättet finns i den här guiden: [Felsöka och Felsök maskin inlärnings pipeliner i Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Felsöka och felsöka i Visual Studio Code

I vissa fall kan du behöva interaktivt felsöka python-koden som används i ML-pipeline. Genom att använda Visual Studio Code (VS Code) och Python Tools for Visual Studio (PTVSD) kan du koppla till koden när den körs i tränings miljön.

### <a name="prerequisites"></a>Krav

* En __Azure Machine Learning arbets yta__ som har kon figurer ATS för att använda en __Azure-Virtual Network__.
* En __Azure Machine Learning pipeline__ som använder Python-skript som en del av stegen i pipelinen. Till exempel en PythonScriptStep.
* Ett Azure Machine Learning beräknings kluster, som finns __i det virtuella nätverket__ och __används av pipelinen för utbildning__.
* En __utvecklings miljö__ som finns __i det virtuella nätverket__. Utvecklings miljön kan vara något av följande:

    * En virtuell Azure-dator i det virtuella nätverket
    * En beräknings instans av den virtuella Notebook-datorn i det virtuella nätverket
    * En klient dator som är ansluten till det virtuella nätverket via ett virtuellt privat nätverk (VPN).

Mer information om hur du använder en Azure-Virtual Network med Azure Machine Learning finns i [skydda Azure ml-experimentering och härlednings jobb i en Azure-Virtual Network](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Så här fungerar det

Dina steg i ML-pipeline kör Python-skript. Dessa skript ändras för att utföra följande åtgärder:
    
1. Logga IP-adressen för den värd som de körs på. Du använder IP-adressen för att ansluta fel sökaren till skriptet.

2. Starta PTVSD debug-komponenten och vänta tills en fel sökare ansluter.

3. I utvecklings miljön övervakar du de loggar som skapats av inlärnings processen för att hitta IP-adressen där skriptet körs.

4. Du anger VS-kod för IP-adressen för att ansluta fel sökaren till med `launch.json` hjälp av en fil.

5. Du kopplar fel söknings programmet och interaktivt steg genom skriptet.

### <a name="configure-python-scripts"></a>Konfigurera Python-skript

Om du vill aktivera fel sökning gör du följande ändringar i python-skripten som används av stegen i ML-pipeline:

1. Lägg till följande import uttryck:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Lägg till följande argument. Med de här argumenten kan du aktivera fel söknings programmet vid behov och ange tids gränsen för att koppla fel söknings programmet:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Lägg till följande-instruktioner. Dessa instruktioner läser in den aktuella körnings kontexten så att du kan logga IP-adressen för noden som koden körs på:

    ```python
    global run
    run = Run.get_context()
    ```

1. Lägg till `if` en instruktion som startar PTVSD och väntar på att ett fel söknings program ska bifogas. Om ingen fel sökare bifogas före tids gränsen fortsätter skriptet som normalt.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

Följande python-exempel visar en grundläggande `train.py` fil som aktiverar fel sökning:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurera ML-pipeline

Om du vill tillhandahålla python-paket som krävs för att starta PTVSD och hämta körnings kontexten skapar `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`du en miljö och anger. Ändra SDK-versionen så att den matchar den som du använder. Följande kodfragment visar hur du skapar en miljö:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

I avsnittet [Konfigurera Python-skript](#configure-python-scripts) , lades två nya argument till i skripten som används av dina steg i ml-pipeline. Följande kodfragment visar hur du använder dessa argument för att aktivera fel sökning för komponenten och ange en tids gräns. Den visar också hur du använder miljön som skapats tidigare genom att `runconfig=run_config`ställa in:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

När pipelinen körs skapar varje steg en underordnad körning. Om fel sökning är aktiverat loggar det ändrade skriptet information som liknar följande text i `70_driver_log.txt` för den underordnade körningen:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Spara `ip_address` värdet. Den används i nästa avsnitt.

> [!TIP]
> Du kan också hitta IP-adressen från körnings loggarna för den underordnade körningen för den här pipeline-steget. Mer information om att visa den här informationen finns i [övervaka Azure ml experiment körningar och mått](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Om du vill installera Python Tools for Visual Studio (PTVSD) i din VS Code Development-miljö använder du följande kommando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Mer information om hur du använder PTVSD med VS Code finns i [fjärrfelsökning](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Om du vill konfigurera VS-kod för att kommunicera med Azure Machine Learning beräkning som kör fel söknings programmet, skapar du en ny fel söknings konfiguration:

    1. Från VS Code väljer du __Felsök__ -menyn och väljer sedan __Öppna konfigurationer__. En fil med namnet __Launch. JSON__ öppnas.

    1. I filen __Launch. JSON__ letar du reda på raden som innehåller `"configurations": [`och infogar följande text efter den. Ändra `"host": "10.3.0.5"` posten till den IP-adress som returnerades i loggarna från föregående avsnitt. Ändra `"localRoot": "${workspaceFolder}/code/step"` posten till en lokal katalog som innehåller en kopia av skriptet som felsöks:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Om det redan finns andra poster i avsnittet konfigurationer lägger du till ett kommatecken (,) efter den kod som du har infogat.

        > [!TIP]
        > Det bästa sättet är att hålla resurserna för skript i separata kataloger, vilket är orsaken `localRoot` till exempel värdes referensen. `/code/step1`
        >
        > Om du felsöker flera skript i olika kataloger skapar du ett separat konfigurations avsnitt för varje skript.

    1. Spara filen __Launch. JSON__ .

### <a name="connect-the-debugger"></a>Anslut fel söknings programmet

1. Öppna VS Code och öppna en lokal kopia av skriptet.
2. Ange Bryt punkter där du vill att skriptet ska stoppa när du har kopplat.
3. Medan den underordnade processen kör skriptet och `Timeout for debug connection` visas i loggarna använder du F5-tangenten eller väljer __Felsök__. När du uppmanas väljer du __Azure Machine Learning Compute: konfiguration av fjärrfelsökning__ . Du kan också välja fel söknings ikonen från sido fältet, __Azure Machine Learning: fjärrfelsöknings__ post i list rutan Felsök och Använd sedan den gröna pilen för att koppla fel sökaren.

    Vid det här tillfället ansluter VS Code till PTVSD på Compute-noden och stoppas vid den Bryt punkt som du har angett tidigare. Nu kan du gå igenom koden när den körs, Visa variabler osv.

    > [!NOTE]
    > Om loggen visar att en `Debugger attached = False`uppgift visas har tids gränsen gått ut och skriptet fortsatte utan fel sökning. Skicka pipelinen igen och Anslut fel söknings programmet efter `Timeout for debug connection` meddelandet och innan tids gränsen upphör att gälla.

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med [azureml-pipeline – Core-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketet och [azureml-pipeline-steg-](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketet.

* Se listan över [designers undantag och felkoder](algorithm-module-reference/designer-error-codes.md).
