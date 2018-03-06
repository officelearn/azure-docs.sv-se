---
title: "Detaljerad vägledning om hur du använder Azure Machine Learning Data förberedelser körningen API | Microsoft Docs"
description: "Det här dokumentet ger information om körning tidigare datakällor och Data förberedelser paket"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Köra datakällor och Data förberedelser paket från Python

Använda en Azure Machine Learning-datakällor eller Azure Machine Learning Data förberedelser paketet i Python:

1. Gå till den **Data** fliken i ditt projekt.

2. Högerklicka på lämplig källa.

3. Välj **generera Data Access kodfilen.**

Den här åtgärden skapar ett kort Python-skript som kör paketet och returnerar ett dataframe.

## <a name="data-sources"></a>Datakällor

Den `azureml.dataprep.datasource` modulen innehåller en funktion för att köra en datakälla och returnera ett dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` är sökvägen till datakällan (.dsource-fil).
- `secrets` är ett valfritt ordbok som mappar nycklar till hemligheter.
- `spark` är ett valfritt bool som anger om du vill returnera ett Spark-dataframe eller en Pandas dataframe. Standard avgör Azure Machine Learning arbetsstationen vilken typ av dataframe returnera vid körning baserat på kontext.

## <a name="data-preparations-packages"></a>Data förberedelser paket

Den `azureml.dataprep.package` modulen innehåller tre funktioner som kör ett dataflöde från ett Data förberedelser paket.

### <a name="execution-functions"></a>Körning av funktioner

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` skickar angivna dataflödet för körning men returnerar inte en dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` Kör det angivna dataflödet och returnerar resultatet som ett dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` Kör angivna dataflödet baserat på en InMemory-datakällan och returnerar resultatet som ett dataframe. Den `user_config` argumentet är en dictionary som mappar den absoluta sökvägen till en datakälla (.dsource-fil) till en InMemory-datakälla som visas i form av en lista med listor.

### <a name="common-arguments"></a>Vanliga argument

- `package_path` är sökvägen till paketet Data förberedelser (.dprep-fil).
- `dataflow_idx` är det nollbaserade indexet för vilka dataflödet i paketet för att köra. Om det angivna dataflödet refererar till andra dataflöden eller datakällor, utförs de också.
- `secrets` är ett valfritt ordbok som mappar nycklar till hemligheter.
- `spark` är ett valfritt bool som anger om du vill returnera ett Spark-dataframe eller en Pandas dataframe. Som standard avgör arbetsstationen vilken typ av dataframe returnera vid körning baserat på kontext.
