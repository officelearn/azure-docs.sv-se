---
title: Felsöka pipelines för maskininlärning
titleSuffix: Azure Machine Learning
description: Felsöka och felsöka pipelines för maskininlärning i Azure Machine Learning SDK för Python. Lär dig vanliga fallgropar för att utveckla pipelines och tips som hjälper dig att felsöka skripten före och under fjärrkörning. Lär dig hur du använder Visual Studio-kod för att interaktivt felsöka dina pipelines för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: b68efbb64e9634ade001373e8cd9d61355bf786f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388992"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Felsöka pipelines för maskininlärning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du felsöker och [felsöker pipelines för maskininlärning](concept-ml-pipelines.md) i [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) och [Azure Machine Learning designer (förhandsversion).](https://docs.microsoft.com/azure/machine-learning/concept-designer) Information ges om hur du:

* Felsöka med Azure Machine Learning SDK
* Felsöka med Azure Machine Learning-designern
* Felsöka med hjälp av programinsikter
* Felsök interaktivt med Visual Studio-kod (VS-kod) och Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Felsöka och felsöka i Azure Machine Learning SDK
Följande avsnitt innehåller en översikt över vanliga fallgropar när du skapar pipelines och olika strategier för felsökning av koden som körs i en pipeline. Använd följande tips när du har problem med att få en pipeline att köras som förväntat.

### <a name="testing-scripts-locally"></a>Testa skript lokalt

Ett av de vanligaste felen i en pipeline är att ett bifogat skript (datarensningsskript, bedömningsskript osv.) inte körs som avsett eller innehåller körningsfel i fjärrberäkningskontexten som är svåra att felsöka på arbetsytan i Azure Machine Lärande studio. 

Pipelines själva kan inte köras lokalt, men om du kör skripten isolerat på den lokala datorn kan du felsöka snabbare eftersom du inte behöver vänta på beräknings- och miljöbyggprocessen. Vissa utvecklingsarbete krävs för att göra detta:

* Om dina data finns i ett molndatalager måste du hämta data och göra dem tillgängliga för skriptet. Använda ett litet urval av dina data är ett bra sätt att skära ner på runtime och snabbt få feedback på skript beteende
* Om du försöker simulera ett mellanliggande pipeline-steg kan du behöva skapa objekttyperna manuellt som det specifika skriptet förväntar sig från föregående steg
* Du måste också definiera din egen miljö och replikera de beroenden som definierats i fjärrberäkningsmiljön

När du har en skriptkonfiguration för att köras på din lokala miljö är det mycket enklare att felsöka uppgifter som:

* Bifoga en anpassad felsökningskonfiguration
* Pausa körning och inspektion av objekttillstånd
* Fånga typ eller logiska fel som inte visas förrän körning

> [!TIP] 
> När du kan verifiera att skriptet körs som förväntat körs ett bra nästa steg skriptet i en pipeline i ett enda steg innan du försöker köra det i en pipeline med flera steg.

### <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärrkontext

Att testa skript lokalt är ett bra sätt att felsöka stora kodfragment och komplex logik innan du börjar bygga en pipeline, men någon gång kommer du sannolikt att behöva felsöka skript under själva pipelinekörningen, särskilt när du diagnostiserar beteenden som uppstår under interaktionen mellan rörledningssteg. Vi rekommenderar liberal `print()` användning av satser i dina stegskript så att du kan se objekttillstånd och förväntade värden under fjärrkörning, på samma sätt som du skulle felsöka JavaScript-kod.

Loggfilen `70_driver_log.txt` innehåller: 

* Alla utskrivna utdrag under skriptets körning
* Stackspårningen för skriptet 

Om du vill hitta den här och andra loggfiler i portalen klickar du först på pipeline-körningen på arbetsytan.

![Listsida för pipelinekörning](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navigera till detaljsidan för pipelinekörning.

![Informationssida för pipelinekörning](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klicka på modulen för det specifika steget. Navigera till fliken **Loggar.** Andra loggar innehåller information om din miljöavbildningsversionsprocess och stegförberedelseskript.

![Fliken Detaljsida för pipelinekörning](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Körningar för *publicerade pipelines* finns på fliken **Slutpunkter** på arbetsytan. Körs för *icke-publicerade pipelines* finns i **Experiment** eller **Pipelines**.

### <a name="troubleshooting-tips"></a>Felsökningstips

Följande tabell innehåller vanliga problem under pipeline utveckling, med potentiella lösningar.

| Problem | Möjlig lösning |
|--|--|
| Det går inte `PipelineData` att skicka data till katalogen | Se till att du har skapat en katalog i skriptet som motsvarar var pipelinen förväntar sig att stegutdata ska användas. I de flesta fall definierar ett indataargument utdatakatalogen och sedan skapar du katalogen explicit. Används `os.makedirs(args.output_dir, exist_ok=True)` för att skapa utdatakatalogen. Se [självstudien](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) för ett exempel på bedömningsskript som visar det här designmönstret. |
| Beroendebuggar | Om du har utvecklat och testat skript lokalt men hittar beroendeproblem när du kör på en fjärrberäkning i pipelinen, se till att dina beräkningsmiljöberoenden och versioner matchar din testmiljö. (Se [Miljöbyggnad, cachelagring och återanvändning](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Tvetydiga fel med beräkningsmål | Om du tar bort och återskapar beräkningsmål kan vissa problem lösas med beräkningsmål. |
| Pipelinen återanvänder inte steg | Steg återanvändning är aktiverat som standard, men se till att du inte har inaktiverat den i ett pipeline-steg. Om återanvändning är `allow_reuse` inaktiverat ställs parametern `False`i steget in på . |
| Pipelinen körs i onödan | Om du vill vara säkra på att stegen bara körs igen när deras underliggande data eller skript ändras frikopplar du katalogerna för varje steg. Om du använder samma källkatalog för flera steg kan det uppstå onödiga repriser. Använd `source_directory` parametern på ett pipelinestegsobjekt för att peka på den isolerade katalogen `source_directory` för det steget och se till att du inte använder samma sökväg för flera steg. |

### <a name="logging-options-and-behavior"></a>Loggningsalternativ och beteende

Tabellen nedan innehåller information om olika felsökningsalternativ för pipelines. Det är inte en uttömmande lista, eftersom andra alternativ finns förutom bara Azure Machine Learning, Python och OpenCensus som visas här.

| Bibliotek                    | Typ   | Exempel                                                          | Mål                                  | Resurser                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Mått | `run.log(name, val)`                                             | Azure Machine Learning Portal användargränssnitt             | [Så här spårar du experiment](how-to-track-experiments.md#available-metrics-to-track)<br>[klassen azureml.core.Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python-utskrift/loggning    | Logga    | `print(val)`<br>`logging.info(message)`                          | Drivrutinsloggar, Azure Machine Learning-designer | [Så här spårar du experiment](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python-loggning](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Logga    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights - spår                | [Felsöka pipelines i Application Insights.](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportörer](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python loggning kokbok](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exempel på loggningsalternativ

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Felsöka och felsöka i Azure Machine Learning designer (förhandsversion)

Det här avsnittet innehåller en översikt över hur du felsöker pipelines i designern.
För pipelines som skapats i designern kan du hitta **loggfilerna** på antingen redigeringssidan eller på detaljsidan för pipelinekörning.

### <a name="access-logs-from-the-authoring-page"></a>Komma åt loggar från redigeringssidan

När du skickar in en pipeline-körning och stannar kvar på redigeringssidan kan du hitta de loggfiler som genereras för varje modul.

1. Välj en modul på redigeringsarbetsytan.
1. Gå till fliken **Utdata + loggar** i den högra rutan i modulen.
1. Markera loggfilen `70_driver_log.txt`.

    ![Redigera sidmodulsloggar](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Åtkomstloggar från pipeline-körningar

Du kan också hitta loggfilerna för specifika körningar på detaljsidan för pipelinekörning i avsnitten **Pipeline eller** **Experiment.**

1. Välj en pipeline-körning som skapats i designern.
    ![Sida för pipelinekörning](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Välj en modul i förhandsgranskningsfönstret.
1. Gå till fliken **Utdata + loggar** i den högra rutan i modulen.
1. Markera loggfilen `70_driver_log.txt`.

## <a name="debug-and-troubleshoot-in-application-insights"></a>Felsöka och felsöka i Application Insights
Mer information om hur du använder OpenCensus Python-biblioteket på det här sättet finns i den här guiden: [Felsöka och felsöka pipelines för maskininlärning i Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Felsöka och felsöka i Visual Studio-kod

I vissa fall kan du behöva felsöka Python-koden som används i ML-pipelinen interaktivt. Genom att använda Visual Studio Code (VS Code) och Python Tools for Visual Studio (PTVSD) kan du koppla till koden när den körs i träningsmiljön.

### <a name="prerequisites"></a>Krav

* En __Azure Machine Learning-arbetsyta__ som är konfigurerad för att använda ett __Virtuellt Azure-nätverk__.
* En __Azure Machine Learning-pipeline__ som använder Python-skript som en del av pipeline-stegen. Till exempel en PythonScriptStep.
* Ett Azure Machine Learning Compute-kluster, som finns __i det virtuella nätverket__ och används av __pipelinen för utbildning__.
* En __utvecklingsmiljö__ som finns __i det virtuella nätverket__. Utvecklingsmiljön kan vara något av följande:

    * En virtuell Azure-dator i det virtuella nätverket
    * En beräkningsinstans av virtuell dator med anteckningsbok i det virtuella nätverket
    * En klientdator som är ansluten till det virtuella nätverket av ett virtuellt privat nätverk (VPN).

Mer information om hur du använder ett Virtuellt Azure-nätverk med Azure Machine Learning finns [i Secure Azure ML-experiment och inferensjobb i ett Virtuellt Azure-nätverk](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Hur det fungerar

I ml-pipeline-stegen körs Python-skript. Dessa skript ändras för att utföra följande åtgärder:
    
1. Logga IP-adressen för värden som de körs på. Du använder IP-adressen för att ansluta felsökningsfelen till skriptet.

2. Starta PTVSD-felsökningskomponenten och vänta på att en felsökare ansluter.

3. Från din utvecklingsmiljö övervakar du loggarna som skapats av utbildningsprocessen för att hitta IP-adressen där skriptet körs.

4. Du berättar för VS-koden ip-adressen att ansluta `launch.json` felsökaren till med hjälp av en fil.

5. Du bifogar felsökaren och interaktivt steg genom skriptet.

### <a name="configure-python-scripts"></a>Konfigurera Python-skript

Om du vill aktivera felsökning gör du följande ändringar i Python-skripten som används av steg i ML-pipelinen:

1. Lägg till följande importsatser:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Lägg till följande argument. Med de här argumenten kan du aktivera felsökningen efter behov och ange tidsgränsen för att koppla felsökningsprogrammet:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Lägg till följande satser. Dessa satser läser in den aktuella körningskontexten så att du kan logga IP-adressen för den nod som koden körs på:

    ```python
    global run
    run = Run.get_context()
    ```

1. Lägg `if` till en sats som startar PTVSD och väntar på att en felsökare ska bifogas. Om ingen felsökare bifogas före timeouten fortsätter skriptet som vanligt.

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

I följande Python-exempel `train.py` visas en grundläggande fil som möjliggör felsökning:

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

Skapa en miljö och ange `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`för att tillhandahålla Python-paket som behövs för att starta PTVSD och få körningskontexten. Ändra SDK-versionen så att den matchar den du använder. Följande kodavsnitt visar hur du skapar en miljö:

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

I avsnittet [Konfigurera Python-skript](#configure-python-scripts) har två nya argument lagts till i skripten som används av ml-pipeline-stegen. Följande kodavsnitt visar hur du använder dessa argument för att aktivera felsökning för komponenten och ange en timeout. Det visar också hur du använder miljön `runconfig=run_config`som skapats tidigare genom att ställa in:

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

När pipelinen körs skapar varje steg en underordnad körning. Om felsökning är aktiverat loggar den ändrade skriptinformationen ungefär följande `70_driver_log.txt` text i för den underordnade körningen:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Spara `ip_address` värdet. Det används i nästa avsnitt.

> [!TIP]
> Du kan också hitta IP-adressen från körningsloggarna för den underordnade körningen för det här pipeline-steget. Mer information om hur du visar den här informationen finns i [Övervaka Azure ML-experimentkörningar och mått](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Så här installerar du Python Tools for Visual Studio (PTVSD) i utvecklingsmiljön VS-kod:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Mer information om hur du använder PTVSD med VS-kod finns i [Fjärrfelsökning](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Om du vill konfigurera VS-kod för att kommunicera med Azure Machine Learning-beräkningen som kör felsökningsprogrammet skapar du en ny felsökningskonfiguration:

    1. Välj menyn __Felsökning__ från VS-kod och välj sedan __Öppna konfigurationer__. En fil med namnet __launch.json__ öppnas.

    1. Leta reda på raden som innehåller `"configurations": [`i filen __launch.json__ och infoga följande text efter den. Ändra `"host": "10.3.0.5"` posten till IP-adressen som returneras i dina loggar från föregående avsnitt. Ändra `"localRoot": "${workspaceFolder}/code/step"` posten till en lokal katalog som innehåller en kopia av skriptet som avbuggas:

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
        > Om det redan finns andra poster i avsnittet konfigurationer lägger du till ett kommatecken (,) efter koden som du infogade.

        > [!TIP]
        > Det bästa är att behålla resurserna för skript i separata `localRoot` kataloger, `/code/step1`vilket är anledningen till att exempelvärdet refererar till .
        >
        > Om du felsöker flera skript skapar du i olika kataloger ett separat konfigurationsavsnitt för varje skript.

    1. Spara __filen launch.json.__

### <a name="connect-the-debugger"></a>Anslut felsökaren

1. Öppna VS-koden och öppna en lokal kopia av skriptet.
2. Ange brytpunkter där du vill att skriptet ska stoppas när du har bifogat det.
3. Medan den underordnade processen kör `Timeout for debug connection` skriptet och visas i loggarna använder du F5-tangenten eller väljer __Felsök__. När du uppmanas till det väljer du __Azure Machine Learning Compute: fjärrfelsökningskonfiguration.__ Du kan också välja felsökningsikonen från sidofältet, __Azure Machine Learning: remote debug-posten__ på rullgardinsmenyn Felsökning och sedan använda den gröna pilen för att bifoga felsökningshanteraren.

    Vid denna punkt ansluter VS-kod till PTVSD på beräkningsnoden och stannar vid brytpunkten som du angav tidigare. Du kan nu gå igenom koden när den körs, visa variabler, etc.

    > [!NOTE]
    > Om loggen visar `Debugger attached = False`en post som anger har tidsgränsen upphört att gälla och skriptet fortsätter utan felsökaren. Skicka pipelinen igen och anslut felsökningsprogrammet efter `Timeout for debug connection` meddelandet och innan tidsgränsen upphör att gälla.

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med [azureml-pipelines-core-paketet](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och [azureml-pipelines-steps-steps-steps-paketet.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Se listan över [designerundantag och felkoder](algorithm-module-reference/designer-error-codes.md).
