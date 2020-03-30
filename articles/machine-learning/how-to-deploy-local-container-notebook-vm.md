---
title: Distribuera modeller för att beräkna instanser
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning-modeller som en webbtjänst med hjälp av beräkningsinstanser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: afbd9950c31bc1c40b01ec0aaf3d2bfffb8a6b94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398252"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Distribuera en modell till Azure Machine Learning-beräkningsinstanser

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webbtjänst på din Azure Machine Learning-beräkningsinstans. Använd beräkningsinstanser om något av följande villkor är sant:

- Du måste snabbt distribuera och validera din modell.
- Du testar en modell som är under utveckling.

> [!TIP]
> Distribuera en modell från en Jupyter Notebook på en beräkningsinstans, till en webbtjänst på samma virtuella dator är en _lokal distribution_. I det här fallet är den "lokala" datorn beräkningsinstansen. Mer information om distributioner finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Krav

- En Azure Machine Learning-arbetsyta med en beräkningsinstans som körs. Mer information finns i [Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Distribuera till beräkningsinstanserna

En exempel anteckningsbok som visar lokala distributioner ingår i din beräkningsinstans. Gör så här för att läsa in anteckningsboken och distribuera modellen som en webbtjänst på den virtuella datorn:

1. Välj dina Azure Machine Learning-beräkningsinstanser från [Azure Machine Learning Studio.](https://ml.azure.com)

1. Öppna `samples-*` underkatalogen och öppna `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`sedan . Kör anteckningsboken när den är öppen.

    ![Skärmbild av den lokala tjänsten som körs på anteckningsbok](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Anteckningsboken visar webbadressen och porten som tjänsten körs på. Till exempel `https://localhost:6789`. Du kan också köra `print('Local service port: {}'.format(local_service.port))` cellen som innehåller för att visa porten.

    ![Skärmbild av den lokala serviceporten som körs](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Om du vill testa tjänsten från `https://localhost:<local_service.port>` en beräkningsinstans använder du URL:en. Om du vill testa från en fjärrklient hämtar du den offentliga URL:en för tjänsten som körs på beräkningsinstansen. Den offentliga URL:en kan bestämmas med hjälp av följande formel. 
    * Bärbar dator `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`VM: . 
    * Beräkningsinstans: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Exempel: 
    * Virtuell dator för bärbara datorer:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Beräkningsinstans:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testa tjänsten

Om du vill skicka exempeldata till den tjänst som körs använder du följande kod. Ersätt värdet `service_url` för med URL:en från föregående steg:

> [!NOTE]
> När du autentiserar till en distribution på beräkningsinstansen görs autentiseringen med Azure Active Directory. Anropet `interactive_auth.get_authentication_header()` till i exempelkoden autentiserar dig med AAD och returnerar ett huvud som sedan kan användas för att autentisera till tjänsten på beräkningsinstansen. Mer information finns i [Konfigurera autentisering för Azure Machine Learning-resurser och arbetsflöden](how-to-setup-authentication.md#interactive-authentication).
>
> När du autentiserar till en distribution på Azure Kubernetes Service eller Azure Container Instances används en annan autentiseringsmetod. Mer information om finns i [Konfigurera autentisering för Azure Machine Learning-resurser och arbetsflöden](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Säkra Azure Machine Learning-webbtjänster med SSL](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)