---
title: Exportera eller ta bort arbetsytedata
titleSuffix: Azure Machine Learning
description: Lär dig hur du exporterar eller tar bort arbetsytan med Azure Machine Learning studio, CLI, SDK och autentiserade REST-API:er.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218293"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportera eller ta bort arbetsytedata för machine learning-tjänsten

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I Azure Machine Learning kan du exportera eller ta bort dina arbetsytedata med det autentiserade REST API:et. I den här artikeln får du lära dig hur du gör.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Styra dina arbetsytedata

Produktdata som lagras av Azure Machine Learning är tillgängliga för export och borttagning via Azure Machine Learning studio, CLI, SDK och autentiserade REST-API:er. Telemetridata kan nås via Azure Privacy-portalen. 

I Azure Machine Learning består personuppgifter av användarinformation i körningshistorikdokument och telemetriposter för vissa användarinteraktioner med tjänsten.

## <a name="delete-workspace-data-with-the-rest-api"></a>Ta bort arbetsytedata med REST API

För att ta bort data kan följande API-anrop göras med HTTP DELETE-verbet. Dessa auktoriseras `Authorization: Bearer <arm-token>` genom att ha `<arm-token>` ett huvud i begäran, var är AAD-åtkomsttoken för `https://management.core.windows.net/` slutpunkten.  

Mer information om hur du hämtar den här token- och anropa Azure-slutpunkterna finns i [Använda REST för att hantera ML-resurser](how-to-manage-rest.md) och Azure REST [API-dokumentation](https://docs.microsoft.com/rest/api/azure/).  

I exemplen nedan ersätter {} du texten med de förekomstnamn som bestämmer den associerade resursen.

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbetsyta

Använd det här anropet om du vill ta bort en hel arbetsyta.  
> [!WARNING]
> All information kommer att tas bort och arbetsytan kommer inte längre att kunna användas.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Ta bort modeller

Använd det här samtalet för att få en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Enskilda modeller kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Ta bort tillgångar

Använd det här anropet för att få en lista över tillgångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Enskilda tillgångar kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Ta bort avbildningar

Använd det här samtalet för att få en lista över bilder och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Enskilda bilder kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Ta bort tjänster

Använd det här samtalet för att få en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Enskilda tjänster kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Exportera tjänstdata med REST API

För att exportera data kan följande API-anrop göras med HTTP GET-verbet. Dessa auktoriseras `Authorization: Bearer <arm-token>` genom att ha `<arm-token>` ett huvud i begäran, var är AAD-åtkomsttoken för slutpunkten`https://management.core.windows.net/`  

Mer information om hur du hämtar den här token- och anropa Azure-slutpunkterna finns i [Använda REST för att hantera ML-resurser](how-to-manage-rest.md) och Azure REST [API-dokumentation](https://docs.microsoft.com/rest/api/azure/)..   

I exemplen nedan ersätter {} du texten med de förekomstnamn som bestämmer den associerade resursen.

### <a name="export-workspace-information"></a>Exportera information om arbetsytan

Använd det här anropet om du vill få en lista över alla arbetsytor:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Information om en enskild arbetsyta kan erhållas genom att:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Exportera beräkningsinformation

Alla beräkningsmål som är kopplade till en arbetsyta kan erhållas genom att:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Information om ett enda beräkningsmål kan erhållas genom att:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Exportera körningshistorikdata

Använd det här samtalet för att få en lista över alla experiment och deras information:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alla körningar för ett visst experiment kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Körhistorikobjekt kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alla körningsmått för ett experiment kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Ett enda körningsmått kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Exportera artefakter

Använd det här anropet om du vill få en lista över artefakter och deras sökvägar:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Exportera meddelanden

Använd det här anropet om du vill få en lista över lagrade uppgifter:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Meddelanden för en enskild uppgift kan erhållas genom att:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportera datalager

Använd det här samtalet för att få en lista över datalager:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Individuella datalager kan erhållas genom:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportera modeller

Använd det här samtalet för att få en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Individuella modeller kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exportera tillgångar

Använd det här anropet för att få en lista över tillgångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Enskilda tillgångar kan erhållas genom att:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Exportera bilder

Använd det här samtalet för att få en lista över bilder och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Enskilda bilder kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exporttjänster

Använd det här samtalet för att få en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Individuella tjänster kan erhållas genom att:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Exportera pipelineexperiment

Individuella experiment kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportera pipelinediagram

Individuella grafer kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportera pipelinemoduler

Moduler kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportera pipeline-mallar

Mallar kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportera pipeline-datakällor

Datakällor kan erhållas genom att:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Ta bort tillgångar i designern

I designern där du skapade experimentet tar du bort enskilda resurser:

1. Gå till designern

    ![Ta bort tillgångar](./media/how-to-export-delete-data/delete-experiment.png)

1. Välj det enskilda pipeline-utkastet som ska tas bort i listan.

1. Välj **Ta bort**.

### <a name="delete-datasets-in-the-designer"></a>Ta bort datauppsättningar i designern

Om du vill ta bort datauppsättningar i designern använder du Azure-portalen eller Storage Explorer för att navigera till anslutna lagringskonton och ta bort datauppsättningar där. Om du avregistrerar datauppsättningar i designern tas endast referenspunkten i lagringen bort.

## <a name="export-data-in-the-designer"></a>Exportera data i designern

I designern där du skapade experimentet exporterar du data som du har lagt till:

1. Till vänster väljer du **Datauppsättningar**.

1. I listan väljer du den datauppsättning som ska exporteras

    ![Nedladdning av data](./media/how-to-export-delete-data/unregister-dataset.png)
