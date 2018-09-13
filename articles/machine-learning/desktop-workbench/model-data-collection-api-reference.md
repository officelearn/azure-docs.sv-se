---
title: Azure Machine Learning Model Data Collection API-referens | Microsoft Docs
description: Azure Machine Learning Model Data Collection API-referens.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: d9fee56d7748cdfd34f982fe79467f7d61c54926
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646826"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Azure Machine Learning Model Data Collection API-referens

Insamling av modelldata kan du arkivera modellindata och förutsägelser från machine learning-webbtjänst. Se den [instruktionsguide för modellen data collection](how-to-use-model-data-collection.md) att förstå hur du installerar `azureml.datacollector` på din Windows- och Linux-dator.

I referenshandboken API använder vi en stegvis metod på hur du samlar in modellindata och förutsägelser från machine learning-webbtjänst.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Aktivera insamling av modelldata i Azure ML Workbench-miljö

 Leta efter conda\_dependencies.yml filen i projektet under mappen aml_config och ha dina conda\_dependencies.yml filen innehålla modulen azureml.datacollector under avsnittet pip enligt nedan. Observera att detta är endast ett underavsnittet för en fullständig conda\_dependencies.yml fil:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>För närvarande kan du använda modulen data collector i Azure ML Workbench genom att köra i docker-läge. Lokalt läge kanske inte fungerar för alla miljöer.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Aktivera insamling av modelldata i bedömningsfil

Importera data collector modulen och ModelDataCollector klass i bedömningsfil som används för driftsättning:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Modellen data collector instansiering
Skapa en instans av en ny instans av en ModelDataCollector:

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Se information om klassen och parametern:

### <a name="class"></a>Klass
| Namn | Beskrivning |
|--------------------|--------------------|
| ModelDataCollector | En klass i azureml.datacollector namnområdet. En instans av den här klassen används för att samla in modelldata. En bedömnings-fil kan innehålla flera ModelDataCollectors. Varje instans som ska användas för att samla in data i en diskret plats i bedömningsfil så att schemat för insamlade data förblir konsekvent (dvs, indata och förutsägelser)|


### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
|-------------|------------|-------------------------|
| modellnamn | sträng | namnet på vilka data som samlas in för modellen |
| identifierare | sträng | platsen i kod som identifierar dessa data, dvs. 'RawInput' eller 'Förutsägelse ” |
| feature_names | lista med strängar | en lista med funktionsnamn som blivit csv-huvud när anges |
| model_management_account_id | sträng | identifierare för modellhanteringskontot där den här modellen lagras. Det fylls i automatiskt när modeller är operationaliserat via AML |
| webservice_name | sträng | namnet på webbtjänsten där den här modellen för närvarande har distribuerats. Det fylls i automatiskt när modeller är operationaliserat via AML |
| model_id | sträng | Den unika identifieraren för den här modellen i samband med ett konto för modellhantering. Det fylls i automatiskt när modeller är operationaliserat via AML |
| model_version | sträng | Det lägre versionsnumret för den här modellen i samband med ett konto för modellhantering. Det fylls i automatiskt när modeller är operationaliserat via AML |



 

## <a name="collecting-the-model-data"></a>Samla in modelldata

Du kan samla in modelldata med hjälp av en instans av ModelDataCollector som skapades ovan.

    dc.collect(input_data, user_correlation_id="")

Se information om metoden och parametern:

### <a name="method"></a>Metod
| Namn | Beskrivning |
|--------------------|--------------------|
| samla in | Används för att samla in data för en modell, indata eller förutsägelse|


### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
|-------------|------------|-------------------------|
| input_data | flera typer | data samlas in (accepterar för närvarande typlistan, numpy.array, pandas. Nu när dataramen, pyspark.sql.DataFrame). För typer av dataramar om det finns en rubrik med funktionsnamn, ingår den här informationen i datamålet (utan att behöva explicit skicka funktionsnamn i konstruktorn ModelDataCollector) |
| user_correlation_id | sträng | ett valfritt Korrelations-id som kan anges av användaren för att kombinera den här förutsägelse |

