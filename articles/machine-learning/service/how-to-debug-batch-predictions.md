---
title: Felsök och felsöka ParallelRunStep
titleSuffix: Azure Machine Learning
description: Felsök och Felsök maskin inlärnings pipeliner i Azure Machine Learning SDK för python. Lär dig vanliga fall GRO par för utveckling med pipelines och tips som hjälper dig att felsöka skript före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852333"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Felsöka och Felsök med hjälp av ParallelRun
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att felsöka och felsöka klassen [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) från [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testa skript lokalt

Se [avsnittet testa skript lokalt](how-to-debug-pipelines.md#testing-scripts-locally) för Machine Learning-pipeliner. Din ParallelRunStep körs som ett steg i ML-pipelines så att samma svar gäller båda.

## <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Över gången från att felsöka ett bedömnings skript lokalt för att felsöka ett bedömnings skript i en faktisk pipeline kan vara ett svårt kliv. Information om hur du hittar dina loggar i portalen finns i [avsnittet Machine Learning-pipeline för att felsöka skript från en fjärran sluten kontext](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informationen i avsnittet gäller även för en körning av en batch-härledning.

Till exempel innehåller logg filen `70_driver_log.txt` också: 

* Alla uttryckta instruktioner under körningen av skriptet.
* Stack spårningen för skriptet.

På grund av den distribuerade karaktären för batch-härlednings jobb finns det loggar från flera olika källor. Två konsoliderade filer skapas dock som tillhandahåller information på hög nivå:

- `~/logs/overview.txt`: den här filen innehåller en information på hög nivå om antalet mini-batchar (även kallade aktiviteter) som skapats hittills och antalet mini-batchar som bearbetats hittills. I det här slutet visas resultatet av jobbet. Om jobbet misslyckades visas fel meddelandet och var du ska starta fel sökningen.

- `~/logs/master.txt`: den här filen ger huvudnoden (kallas även Orchestrator) vyn för jobbet som körs. Innehåller skapande av aktiviteter, förlopps övervakning, körnings resultat.

När du behöver en fullständig förståelse för hur varje nod kör Poäng skriptet kan du titta på de enskilda process loggarna för varje nod. Process loggarna finns i mappen `worker`, grupperade efter arbetsnoder:

- `~/logs/worker/<ip_address>/Process-*.txt`: den här filen innehåller detaljerad information om varje mini-batch som den hämtas eller slutförs av en anställd. För varje mini-batch innehåller den här filen:

    - IP-adressen och PID för arbets processen. 
    - Det totala antalet objekt och antalet objekt som har bearbetats. 
    - Start-och slut tid i vägg klock tider (`start1` och `end1`). 
    - Start-och slut tid i processor åtgången (`start2` och `end2`). 

Du kan också hitta information om resursanvändningen för processerna för varje arbets tagare. Den här informationen är i CSV-format och finns på `~/logs/performance/<ip_address>/`. Om du till exempel söker efter resursutnyttjande kan du titta på följande filer:

- `process_resource_monitor_<ip>_<pid>.csv`: användning av resursanvändning per arbets process. 
- `sys_resource_monitor_<ip>.csv`: per Node-logg.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hur gör jag för att logg från mitt användar skript från en fjärran sluten kontext?
Du kan konfigurera en loggare med stegen nedan för att göra loggarna visas i mappen **loggar/användare** på portalen:
1. Spara det första kod avsnittet nedan i filen entry_script_helper. py och Lägg filen i samma mapp som ditt Entry-skript. Den här klassen hämtar sökvägen inuti AmlCompute. För lokalt test kan du ändra `get_working_dir()` för att returnera en lokal mapp.
2. Konfigurera en loggare i `init()` metod och Använd den sedan. Det andra kod avsnittet nedan är ett exempel. 

**entry_script_helper. py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Ett exempel på ett post skript med hjälp av loggarna:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med paketet [azureml-contrib-pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) och [dokumentationen](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) för ParallelRunStep-klassen.

* Följ den [avancerade självstudien](tutorial-pipeline-batch-scoring-classification.md) om hur du använder pipelines för batch-poäng.
