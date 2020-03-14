---
title: Händelse schema för Azure Event Grid Machine Learning
description: Beskriver de egenskaper som har angetts för Machine Learning-arbetsyta händelser med Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79202152"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Event Grid händelse schema för Azure Machine Learning

Den här artikeln innehåller egenskaper och schema för händelser i Machine Learning-arbetsytan. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md).

En lista över exempel på skript och självstudier finns i [händelse källan för azureml](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Tillgängliga händelse typer

Azure Machine Learning avger följande händelse typer:

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Utlöses när en ny modell eller modell version har registrerats. |
| Microsoft. MachineLearningServices. ModelDeployed | Utlöses när modell (er) har distribuerats till en slut punkt. |
| Microsoft. MachineLearningServices. RunCompleted | Utlöses när en körning har slutförts. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Utlöses när en data uppsättnings drivgarn identifierar driften. |
| Microsoft. MachineLearningServices. RunStatusChanged | Utlöses när en körnings status ändras till "misslyckades". |

## <a name="the-contents-of-an-event-response"></a>Innehållet i ett händelse svar

När en händelse utlöses skickar Event Grid-tjänsten data om händelsen för att prenumerera på slut punkten.

Det här avsnittet innehåller ett exempel på hur data ska se ut för varje händelse.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft. MachineLearningServices. ModelRegistered-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft. MachineLearningServices. ModelDeployed-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft. MachineLearningServices. RunCompleted-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft. MachineLearningServices. DatasetDriftDetected-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft. MachineLearningServices. RunStatusChanged-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| subject | sträng | Publisher-definierad sökväg till händelsens ämne. |
| eventType | sträng | En av de registrerade händelse typerna för den här händelse källan. |
| eventTime | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Händelse data för Blob Storage. |
| dataVersion | sträng | Data objektets schema version. Utgivaren definierar schema versionen. |
| metadataVersion | sträng | Schema versionen för händelsens metadata. Event Grid definierar schemat för de högsta nivå egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper för varje händelse typ:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ModelName | sträng | Namnet på den modell som har registrerats. |
| ModelVersion | sträng | Den version av modellen som har registrerats. |
| ModelTags | objekt | Taggarna för den modell som har registrerats. |
| ModelProperties | objekt | Egenskaperna för den modell som har registrerats. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ServiceName | sträng | Namnet på den distribuerade tjänsten. |
| ServiceComputeType | sträng | Compute-typen (t. ex. ACI, AKS) för den distribuerade tjänsten. |
  | ModelIds | sträng | En kommaavgränsad lista med modell-ID: n. ID: na för de modeller som distribueras i tjänsten. |
| ServiceTags | objekt | Den distribuerade tjänstens taggar. |
| ServiceProperties | objekt | Egenskaperna för den distribuerade tjänsten. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ExperimentId | sträng | ID för experimentet som körningen tillhör. |
| ExperimentName | sträng | Namnet på experimentet som körningen tillhör. |
| RunId | sträng | ID för körningen som har slutförts. |
| RunType | sträng | Körnings typen för den slutförda körningen. |
| RunTags | objekt | Taggarna för den slutförda körningen. |
| RunProperties | objekt | Egenskaperna för den slutförda körningen. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| DataDriftId | sträng | ID för data avvikelse övervakaren som utlöste händelsen. |
| DataDriftName | sträng | Namnet på den data avvikelse Övervakare som utlöste händelsen. |
| RunId | sträng | ID: t för den körning som identifierade data drift. |
| BaseDatasetId | sträng | ID för bas data uppsättningen som används för att identifiera driften. |
| TargetDatasetId | sträng | ID: t för den mål data uppsättning som används för att identifiera driften. |
| DriftCoefficient | double | Det koefficients resultat som utlöste händelsen. |
| StartTime | datetime | Start tiden för den mål data uppsättnings tids serie som ledde till drifts identifiering.  |
| EndTime | datetime | Slut tiden för mål data uppsättningens tids serier som ledde till drifts identifiering. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft. MachineLearningServices. RunStatusChanged

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ExperimentId | sträng | ID för experimentet som körningen tillhör. |
| ExperimentName | sträng | Namnet på experimentet som körningen tillhör. |
| RunId | sträng | ID för körningen som har slutförts. |
| RunType | sträng | Körnings typen för den slutförda körningen. |
| RunTags | objekt | Taggarna för den slutförda körningen. |
| RunProperties | objekt | Egenskaperna för den slutförda körningen. |
| RunStatus | sträng | Status för körningen. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md)
* En introduktion till att använda Azure Event Grid med Azure Machine Learning finns i [använda Azure Machine Learning händelser](/azure/machine-learning/service/concept-event-grid-integration)
* Ett exempel på hur du använder Azure Event Grid med Azure Machine Learning finns i [Skapa event drived Machine Learning-arbetsflöden](/azure/machine-learning/service/how-to-use-event-grid)
