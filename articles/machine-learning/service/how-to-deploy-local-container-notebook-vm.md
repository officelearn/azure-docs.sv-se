---
title: Distribuera modeller till beräknings instanser
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning modeller som en webb tjänst med hjälp av beräknings instanser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: bb187826250b3edc9ac3d9e36a243d75819a45b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496876"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Distribuera en modell för att Azure Machine Learning beräknings instanser

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Beräknings instanser är endast tillgängliga för arbets ytor med en region i **norra centrala USA** eller **Storbritannien, södra**.
>Om din arbets yta finns i en annan region kan du fortsätta att skapa och använda en [virtuell dator](concept-compute-instance.md#notebookvm) i stället.  Du kan distribuera en modell till antingen en beräknings instans eller en virtuell dator med hjälp av stegen i den här artikeln.

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på din Azure Machine Learning beräknings instans. Använd beräknings instanser om något av följande villkor är uppfyllt:

- Du måste snabbt distribuera och validera din modell.
- Du testar en modell som är under utveckling.

> [!TIP]
> Att distribuera en modell från en Jupyter Notebook på en beräknings instans till en webb tjänst på samma virtuella dator är en _lokal distribution_. I det här fallet är den lokala datorn beräknings instansen. Mer information om distributioner finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta med en beräknings instans som körs. Mer information finns i [installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Distribuera till beräknings instanserna

En exempel-anteckningsbok som visar lokala distributioner ingår i din beräknings instans. Använd följande steg för att läsa in den bärbara datorn och distribuera modellen som en webb tjänst på den virtuella datorn:

1. Välj dina Azure Machine Learning beräknings instanser från [Azure Machine Learning Studio](https://ml.azure.com).

1. Öppna under katalogen `samples-*` och öppna `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Kör antecknings boken när den är öppen.

    ![Skärm bild av den lokala tjänsten som körs på antecknings boken](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Antecknings boken visar den URL och port som tjänsten körs på. Till exempel `https://localhost:6789`. Du kan också köra cellen som innehåller `print('Local service port: {}'.format(local_service.port))` för att Visa porten.

    ![Skärm bild av den aktiva porten för den lokala tjänsten](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Använd `https://localhost:<local_service.port>` URL för att testa tjänsten från en beräknings instans. Om du vill testa från en fjärran sluten klient hämtar du den offentliga URL: en för tjänsten som körs på beräknings instansen. Den offentliga URL: en kan fastställas med hjälp av följande formel. 
    * VM för virtuell dator: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Beräknings instans: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 
    
    Exempel: 
    * VM för virtuell dator: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Beräknings instans: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testa tjänsten

Använd följande kod för att skicka exempel data till den aktiva tjänsten. Ersätt värdet för `service_url` med URL: en från föregående steg:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Nästa steg

* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Skydda Azure Machine Learning webb tjänster med SSL](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)