---
title: Exportera eller ta bort data i arbetsytan
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du exporterar eller tar bort din arbets yta med de Azure Portal-, CLI-, SDK-och autentiserade REST-API: erna.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 18e2ab18dac214e73eaf6ad7dfcb9dbbab0b5cf5
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002847"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportera eller ta bort arbetsyta för Machine Learning tjänstdata 

Du kan exportera eller ta bort dina data i arbetsytan med autentiserade REST-API i Azure Machine Learning. Den här artikeln visar hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrollera dina arbetsytedata
Produkt data som lagras av Azure Machine Learning kan exporteras och tas bort via Azure Portal, CLI, SDK och autentiserade REST-API: er. Dessa data kan nås via sekretess för Azure-portalen. 

I Azure Machine Learning består personliga data av användar information i historik dokument och telemetri poster för vissa användar interaktioner med tjänsten.

## <a name="delete-workspace-data-with-the-rest-api"></a>Ta bort arbetsytedata med REST API 

Följande API-anrop kan göras med HTTP DELETE-verb för att ta bort data. De har behörighet genom att låta en `Authorization: Bearer <arm-token>` huvudet i begäran, där `<arm-token>` är AAD-åtkomsttoken för den `https://management.core.windows.net/` slutpunkt.  

Läs hur du får den här token och anropa Azure-slutpunkter i [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/azure/).  

I följande exempel ersätter du texten i {} med instansnamn som bestämmer den associerade resursen.

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbetsyta

Använd det här anropet för att ta bort en hela arbetsytan.  
> [!WARNING]
> All information som kommer att tas bort och arbetsytan inte längre användas.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Ta bort modeller

Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Enskilda modeller kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Ta bort tillgångar

Använd det här anropet för att hämta en lista över tillgångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Enskilda tillgångar kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Ta bort avbildningar

Använd det här anropet för att hämta en lista över avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Enskilda bilder kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Ta bort tjänster

Använd det här anropet för att hämta en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Enskilda tjänster kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportera service data med REST API

Om du vill exportera data kan följande API-anrop göras med HTTP GET-verb. De har behörighet genom att låta en `Authorization: Bearer <arm-token>` huvudet i begäran, där `<arm-token>` är AAD-åtkomsttoken för slutpunkten `https://management.core.windows.net/`  

Läs hur du får den här token och anropa Azure-slutpunkter i [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/azure/).   

I följande exempel ersätter du texten i {} med instansnamn som bestämmer den associerade resursen.

### <a name="export-workspace-information"></a>Exportera information om arbetsytan

Använd det här anropet för att hämta en lista över alla arbetsytor:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Information om en enskild arbetsyta kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportera beräkning Information

Alla beräkningsresurser mål som är kopplade till en arbetsyta kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Information om en enda beräkningsmål kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Historikdata för exportkörning
Använd det här anropet för att hämta en lista över alla experiment och deras information:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alla körningar för ett visst experiment kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Körningshistorik objekt kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alla kör mått för ett experiment kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Ett kör mått kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportera artefakter

Använd det här anropet för att hämta en lista över artefakter och deras sökvägar:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportera meddelanden

Använd det här anropet för att hämta en lista över lagrade uppgifter:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Meddelanden för en enskild uppgift kan erhållas genom:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportera datalager

Använd det här anropet för att hämta en lista över datalager:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Enskilda datalager kan erhållas genom:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportera modeller

Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Enskilda modeller kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportera tillgångar

Använd det här anropet för att hämta en lista över tillgångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Enskilda tillgångar kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exportera bilder

Använd det här anropet för att hämta en lista över avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Enskilda bilder kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exportera tjänster

Använd det här anropet för att hämta en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Enskilda tjänster kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exportera Pipeline experiment

Enskilda experiment kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportera Pipeline-diagram

Enskilda diagram kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportera Pipelinemoduler

Moduler kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportera Pipeline-mallar

Mallar kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportera Pipeline-datakällor

Datakällor kan erhållas genom:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-visual-interface-assets"></a>Ta bort Visual Interface-till gångar

Ta bort enskilda till gångar i det visuella gränssnittet där du skapade experimentet:

1. Välj den typ av till gång som du vill ta bort till vänster.

    ![Ta bort tillgångar](media/how-to-export-delete-data.md/delete-experiment.png)

1. I listan väljer du de enskilda till gångar som ska tas bort.

1. Klicka på **ta bort**längst ned.

## <a name="export-visual-interface-data"></a>Exportera Visual Interface-data

Exportera data som du har lagt till i det visuella gränssnittet där du skapade experimentet:

1. Välj **data**till vänster.

1. Välj **mina data uppsättningar** eller **exempel** på översta sidan för att hitta de data som du vill exportera.

    ![Hämta data](media/how-to-export-delete-data.md/download-data.png)

1. I listan väljer du de enskilda data uppsättningar som ska exporteras.

1. Välj **Ladda ned**på slutet.