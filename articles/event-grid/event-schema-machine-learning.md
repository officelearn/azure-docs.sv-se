---
title: Evenemangsschema för Azure Event Grid Machine Learning
description: Beskriver de egenskaper som tillhandahålls för Machine Learning Workspace-händelser med Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202152"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Event Grid-händelseschema för Azure Machine Learning

Den här artikeln innehåller egenskaper och schema för maskininlärningsarbetsytahändelser. En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

En lista över exempelskript och självstudier finns i [AzureML-händelsekälla](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure Machine Learning avger följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistrerad | Utlöses när en ny modell eller modellversion har registrerats. |
| Microsoft.MachineLearningServices.ModelDeployed | Utlöses när modeller har distribuerats till en slutpunkt. |
| Microsoft.MachineLearningServices.RunCompleted | Utlöses när en körning har slutförts. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Utlöses när en Dataset drift monitor upptäcker drift. |
| Microsoft.MachineLearningServices.RunStatusChanged Microsoft.MachineLearningServices.RunStatusChanged Microsoft.MachineLearningServices.RunStatusChanged Microsoft. | Utlöses när en körningsstatus ändras till "misslyckades". |

## <a name="the-contents-of-an-event-response"></a>Innehållet i ett händelsesvar

När en händelse utlöses skickar tjänsten Event Grid data om händelsen för att prenumerera på slutpunkten.

Det här avsnittet innehåller ett exempel på hur dessa data skulle se ut för varje händelse.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft.MachineLearningServices.ModelRegistrerad händelse

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft.MachineLearningServices.ModelDeployed händelse

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft.MachineLearningServices.RunCompleted-händelse

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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft.MachineLearningServices.DatasetDriftDetected händelse

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

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Microsoft.MachineLearningServices.RunStatusChanged händelse

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




## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för bloblagring. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper för varje händelsetyp:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistrerad

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ModelName | sträng | Namnet på modellen som registrerades. |
| ModellVersion | sträng | Den version av modellen som registrerades. |
| ModelTags (ModellTaggar) | objekt | Taggarna för modellen som registrerades. |
| ModellEgenskaper | objekt | Egenskaperna för modellen som registrerades. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ServiceName | sträng | Namnet på den distribuerade tjänsten. |
| ServiceComputeType | sträng | Beräkningstypen (t.ex. |
  | ModelIds (Modellera) | sträng | En kommaavgränsad lista över modell-ID: er. ID:erna för de modeller som distribueras i tjänsten. |
| ServiceTaggar | objekt | Taggarna för den distribuerade tjänsten. |
| ServiceEgenskaper | objekt | Egenskaperna för den distribuerade tjänsten. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ExperimentId (På) | sträng | ID:et för experimentet som körningen tillhör. |
| ExperimentName (ExperimentName) | sträng | Namnet på experimentet som körningen tillhör. |
| RunId (på) | sträng | ID:et för körningen som slutfördes. |
| RunType (olikartade) | sträng | Körningstypen för den slutförda körningen. |
| RunTags (kör) | objekt | Taggarna för den slutförda körningen. |
| RunProperties (Lö tillbakaEgenskaper) | objekt | Egenskaperna för den slutförda körningen. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| DataDriftId (DataDriftId) | sträng | ID:t för dataavdriftövervakaren som utlöste händelsen. |
| DataDriftName | sträng | Namnet på dataavdrift övervakaren som utlöste händelsen. |
| RunId (på) | sträng | ID:et för körningen som identifierade data driver. |
| BaseDatasetId | sträng | ID:n för basdatauppsättningen som används för att identifiera drift. |
| TargetDatasetId | sträng | ID för målet Dataset som används för att identifiera drift. |
| DriftKoefficient | double | Koefficientens resultat som utlöste händelsen. |
| StartTime | datetime | Starttiden för måldatauppsättningens tidsserier som resulterade i driftidentifiering.  |
| EndTime | datetime | Sluttiden för måldatauppsättningens tidsserier som resulterade i driftidentifiering. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusChanged Microsoft.MachineLearningServices.RunStatusChanged Microsoft.MachineLearningServices.RunStatusChanged Microsoft.

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ExperimentId (På) | sträng | ID:et för experimentet som körningen tillhör. |
| ExperimentName (ExperimentName) | sträng | Namnet på experimentet som körningen tillhör. |
| RunId (på) | sträng | ID:et för körningen som slutfördes. |
| RunType (olikartade) | sträng | Körningstypen för den slutförda körningen. |
| RunTags (kör) | objekt | Taggarna för den slutförda körningen. |
| RunProperties (Lö tillbakaEgenskaper) | objekt | Egenskaperna för den slutförda körningen. |
| Körstatus | sträng | Status för körningen. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md)
* En introduktion till hur du använder Azure Event Grid med Azure Machine Learning finns [i Använda Azure Machine Learning-händelser](/azure/machine-learning/service/concept-event-grid-integration)
* Ett exempel på hur du använder Azure Event Grid med Azure Machine Learning finns i [Skapa händelsedrivna maskininlärningsarbetsflöden](/azure/machine-learning/service/how-to-use-event-grid)
