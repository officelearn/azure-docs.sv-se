---
title: Utförliga instruktioner om hur du använder Azure Machine Learning-Dataförberedelser körningen API | Microsoft Docs
description: Det här dokumentet innehåller information om hur du kör tidigare utformats datakällor och Dataförberedelser paket
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9f15f949ba76240da2788c1a01e7086ba3b42fd1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648939"
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Körning av datakällor och Dataförberedelser-paket från Python

Använda ett Azure Machine Learning-datakällor eller Dataförberedelser för Azure Machine Learning-paket i Python:

1. Gå till den **Data** fliken i ditt projekt.

2. Högerklicka på lämplig källa.

3. Välj **generera kodfil för dataåtkomst.**

Den här åtgärden skapar ett kort Python-skript som körs paketet och returnerar en dataram.

## <a name="data-sources"></a>Datakällor

Den `azureml.dataprep.datasource` modulen innehåller en funktion för att köra en datakälla och returnera en dataram: `load_datasource(path, secrets=None, spark=None)`.
- `path` är sökvägen till datakällan (.dsource-fil).
- `secrets` är ett valfritt ordlista som mappar nycklar till hemligheter.
- `spark` är ett valfritt bool som anger om du vill returnera en Spark dataframe eller en Pandas-dataframe. Som standard anger vilka typer av dataramar ska returneras vid körning baserat på kontexten i Azure Machine Learning Workbench.

## <a name="data-preparations-packages"></a>Data förberedelser paket

Den `azureml.dataprep.package` modulen innehåller tre funktioner som kör ett dataflöde från ett Dataförberedelser-paket.

### <a name="execution-functions"></a>Funktioner för körning

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` skickar angivna dataflödet för körning av men som returnerar inte en dataram.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` Kör det angivna dataflödet och returnerar resultatet som en dataram.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` Kör angivna dataflödet baserat på en InMemory-datakälla och returnerar resultatet som en dataram. Den `user_config` argumentet är en ordlista som mappar den absoluta sökvägen till en datakälla (.dsource-fil) till en InMemory-datakälla som representeras som en lista med listor.

### <a name="common-arguments"></a>Vanliga argument

- `package_path` är sökvägen till Dataförberedelser paketet (.dprep-fil).
- `dataflow_idx` är det nollbaserade indexet för vilka dataflöde i paketet för att köra. Om det angivna dataflödet hänvisar till andra dataflöden eller datakällor, körs de också.
- `secrets` är ett valfritt ordlista som mappar nycklar till hemligheter.
- `spark` är ett valfritt bool som anger om du vill returnera en Spark dataframe eller en Pandas-dataframe. Workbench fastställer vilka typer av dataramar ska returneras vid körning baserat på kontext som standard.
