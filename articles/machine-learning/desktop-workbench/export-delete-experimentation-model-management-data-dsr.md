---
title: Exportera eller ta bort experimentering eller modellera hanteringsdata – Azure Machine Learning | Microsoft Docs
description: 'Du kan exportera eller ta bort ditt kontodata som rör hantering av experimentering eller modell med Azure-portalen, CLI, SDK och autentiserade REST API: er i Azure Machine Learning. Den här artikeln visar hur du gör.'
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 8c5b20bf837491e8b15f7bc8baa018ead584616b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159184"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Exportera eller ta bort din experimentering eller hantering av modelldata i Machine Learning

>[!Note]
>**Den här artikeln är inaktuell.** Stöd för tidigare versioner av den här tjänsten upphör inkrementellt. [Visa tidslinje för support](../service/overview-what-happened-to-workbench.md#timeline). Läs den [senaste artikeln](../service/how-to-export-delete-data.md) på det här avsnittet.


Du kan exportera eller ta bort ditt kontodata som rör hantering av experimentering eller modellen med autentiserade REST-API i Azure Machine Learning. Den här artikeln visar hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Kontrollera dina kontodata
I produkten data som lagras av hantering av Azure Machine Learning-experiment och modeller är tillgängliga för export eller tas bort via Azure portal, CLI, SDK och autentiserade REST API: er. Dessa data kan nås via sekretess för Azure-portalen. 

I Azure Machine Learning består personliga data med användarinformation i körningshistoriken dokument och telemetri poster i vissa användarinteraktioner med tjänsten.

## <a name="delete-account-data-with-the-rest-api"></a>Ta bort kontodata med REST API 

Följande API-anrop kan göras med HTTP DELETE-verb för att ta bort data. De har behörighet genom att låta en `Authorization: Bearer <arm-token>` huvudet i begäran, där `<arm-token>` är AAD-åtkomsttoken för slutpunkten `https://management.core.windows.net/` slutpunkt.  

Läs hur du får den här token och anropa Azure-slutpunkter i [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/azure/).  

I följande exempel ersätter du texten i {} med instansnamn som bestämmer den associerade resursen.

## <a name="delete-from-a-hosting-account"></a>Ta bort från en värd konto

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Ta bort från modellen management-tjänsten

### <a name="model-document"></a>Modelldokument
Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Enskilda modeller kan tas bort med:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Manifest dokumentet
Använd det här anropet för att hämta en lista över alla manifest och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Enskilda manifest kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Service-dokument
Använd det här anropet för att hämta en lista över alla tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Enskilda tjänster kan tas bort med:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Bilddokument
Använd det här anropet för att hämta en lista över alla avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Enskilda bilder kan tas bort med:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Ta bort data som körts historik och artefakt-meddelande
Kör historik och artefakt meddelande stores för ett projekt tas bort när du tar bort motsvarande project-dokument:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Ta bort från resursprovidern konto för experimentering
Projektdokument tas bort med:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Arbetsytan dokument tas bort med:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Hela experimenteringskontot tas bort med:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Exportera service data med REST API
Om du vill exportera data kan följande API-anrop göras med HTTP GET-verb. De har behörighet genom att låta en `Authorization: Bearer <arm-token>` huvudet i begäran, där `<arm-token>` är AAD-åtkomsttoken för slutpunkten `https://management.core.windows.net/`  

Läs hur du får den här token och anropa Azure-slutpunkter i [Azure REST API-dokumentation](https://docs.microsoft.com/rest/api/azure/).   

I följande exempel ersätter du texten i {} med instansnamn som bestämmer den associerade resursen.

## <a name="export-hosting-account-data"></a>Exportera som värd-kontodata

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Exportera modelldata management-tjänsten
### <a name="model-document"></a>Modelldokument

Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Enskilda modeller kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifest
Använd det här anropet för att hämta en lista över alla manifest och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Enskilda manifest kan erhållas genom:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Tjänster
Använd det här anropet för att hämta en lista över alla tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Enskilda tjänster kan erhållas genom: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Avbildningar
Använd det här anropet för att hämta en lista över alla avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Enskilda tjänster kan erhållas genom: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Exportera data för beräkning
### <a name="compute-clusters"></a>Beräkningskluster
Använd det här anropet för att hämta en lista över alla beräkningskluster och deras namn:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Enskilda kluster kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Kluster för driftsättning
Använd det här anropet för att hämta en lista över alla kluster och deras namn:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Enskilda kluster kan erhållas genom:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Historikdata för exportkörning
Använd det här anropet för att hämta en lista över alla körningar och deras ID:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Använd det här anropet för att hämta en lista över alla experiment och deras ID:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Körningshistorik objekt kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Mått som objekt kan erhållas genom att köra:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Kör experiment kan erhållas genom:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Kör historik artefakter:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Kör historik artefakter URI: er:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Exportera artefakter
Använd det här anropet för att hämta en lista över tillgångar och deras namn:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Använd det här anropet för att hämta en lista över artefakter och deras sökvägar:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Artefakten innehållet

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Artefakten dokument

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Tillgångar

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Exportera meddelanden

1. Gå till den [i avsnittet för användare i Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/), och välj sedan en användare från den **namn** kolumn. 
2. Obs den **objekt-ID**, och använda den i följande anrop:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Exportera kontoinformation för experimentering
### <a name="experimentation-account-information"></a>Kontoinformation för experimentering

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Arbetsyteinformation

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Projektinformation

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
