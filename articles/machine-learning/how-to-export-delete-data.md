---
title: Exportera eller ta bort data i arbetsytan
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du exporterar eller tar bort din arbets yta med Azure Machine Learning Studio-, CLI-, SDK-och autentiserade REST-API: er.'
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218293"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportera eller ta bort arbetsyta för Machine Learning tjänstdata

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Du kan exportera eller ta bort dina data i arbetsytan med autentiserade REST-API i Azure Machine Learning. Den här artikeln visar hur du gör.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrollera dina arbetsytedata

Produkt data som lagras av Azure Machine Learning kan exporteras och tas bort via Azure Machine Learning Studio-, CLI-, SDK-och autentiserade REST-API: er. Dessa data kan nås via sekretess för Azure-portalen. 

I Azure Machine Learning består personliga data av användar information i historik dokument och telemetri poster för vissa användar interaktioner med tjänsten.

## <a name="delete-workspace-data-with-the-rest-api"></a>Ta bort arbetsytedata med REST API

Följande API-anrop kan göras med HTTP DELETE-verb för att ta bort data. De är auktoriserade genom att ha ett `Authorization: Bearer <arm-token>`-huvud i begäran där `<arm-token>` är AAD-åtkomsttoken för `https://management.core.windows.net/` slut punkten.  

Information om hur du hämtar denna token och anropar Azure-slutpunkter finns i [använda rest för att hantera ml-resurser](how-to-manage-rest.md) och [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/azure/).  

I exemplen nedan ersätter du texten i {} med de instans namn som avgör den associerade resursen.

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbetsyta

Använd det här anropet för att ta bort en hela arbetsytan.  
> [!WARNING]
> All information som kommer att tas bort och arbetsytan inte längre användas.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Ta bort modeller

Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Enskilda modeller kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Ta bort tillgångar

Använd det här anropet för att hämta en lista över tillgångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Enskilda tillgångar kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Ta bort avbildningar

Använd det här anropet för att hämta en lista över avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Enskilda bilder kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Ta bort tjänster

Använd det här anropet för att hämta en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Enskilda tjänster kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Exportera service data med REST API

Om du vill exportera data kan följande API-anrop göras med HTTP GET-verb. De är auktoriserade genom att ha ett `Authorization: Bearer <arm-token>`-huvud i begäran där `<arm-token>` är AAD-åtkomsttoken för slut punkten `https://management.core.windows.net/`  

Information om hur du hämtar denna token och anropar Azure-slutpunkter finns i [använda rest för att hantera ml-resurser](how-to-manage-rest.md) och [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/azure/)..   

I exemplen nedan ersätter du texten i {} med de instans namn som avgör den associerade resursen.

### <a name="export-workspace-information"></a>Exportera information om arbetsytan

Använd det här anropet för att hämta en lista över alla arbetsytor:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Information om en enskild arbetsyta kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Exportera beräkning Information

Alla beräkningsresurser mål som är kopplade till en arbetsyta kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Information om en enda beräkningsmål kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

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

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Enskilda modeller kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exportera tillgångar

Använd det här anropet för att hämta en lista över tillgångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Enskilda tillgångar kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Exportera bilder

Använd det här anropet för att hämta en lista över avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Enskilda bilder kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exportera tjänster

Använd det här anropet för att hämta en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Enskilda tjänster kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

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

## <a name="delete-assets-in-the-designer"></a>Ta bort till gångar i designern

Ta bort enskilda till gångar i designern där du skapade experimentet:

1. Gå till designern

    ![Ta bort tillgångar](./media/how-to-export-delete-data/delete-experiment.png)

1. I listan väljer du det enskilda pipeline-utkastet som ska tas bort.

1. Välj **Ta bort**.

### <a name="delete-datasets-in-the-designer"></a>Ta bort data uppsättningar i designern

Om du vill ta bort data uppsättningar i designern använder du Azure Portal eller Storage Explorer för att navigera till anslutna lagrings konton och ta bort data uppsättningar där. När data uppsättningar avregistreras i designern tas endast referens punkten i lagring bort.

## <a name="export-data-in-the-designer"></a>Exportera data i designern

Exportera data som du har lagt till i designern där du skapade experimentet:

1. Välj **data uppsättningar**till vänster.

1. I listan väljer du den data uppsättning som ska exporteras

    ![Hämta data](./media/how-to-export-delete-data/unregister-dataset.png)
