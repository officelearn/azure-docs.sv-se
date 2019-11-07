---
title: Så här distribuerar du modeller till Notebook VM
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning-modeller som en webb tjänst med virtuella notebook-datorer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: d4e37b02b3d7a21546a04c8948fbbfb7262bfa6a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584776"
---
# <a name="deploy-a-model-to-azure-machine-learning-notebook-vms"></a>Distribuera en modell till Azure Machine Learning virtuella notebook-datorer

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på din Azure Machine Learning Notebook VM. Använd Notebook-VM: ar om något av följande villkor är uppfyllt:

- Du måste snabbt distribuera och validera din modell.
- Du testar en modell som är under utveckling.

> [!TIP]
> Distribution av en modell från en Jupyter Notebook på en virtuell dator med en virtuell dator, till en webb tjänst på samma virtuella dator är en _lokal distribution_. I det här fallet är den lokala datorn den virtuella Notebook-datorn. Mer information om distributioner finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure Machine Learning arbets yta med en VM-dator som körs. Mer information finns i [installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Distribuera till de virtuella datorerna för bärbara datorer

En exempel-anteckningsbok som visar lokala distributioner ingår i den virtuella datorns bärbara dator. Använd följande steg för att läsa in den bärbara datorn och distribuera modellen som en webb tjänst på den virtuella datorn:

1. Från [Azure Machine Learning Studio](https://ml.azure.com)väljer du Azure Machine Learning Notebook-VM: ar.

1. Öppna under katalogen `samples-*` och öppna `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Kör antecknings boken när den är öppen.

    ![Skärm bild av den lokala tjänsten som körs på antecknings boken](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Antecknings boken visar den URL och port som tjänsten körs på. Till exempel `https://localhost:6789`. Du kan också köra cellen som innehåller `print('Local service port: {}'.format(local_service.port))` för att Visa porten.

    ![Skärm bild av den aktiva porten för den lokala tjänsten](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Använd `https://localhost:<local_service.port>` URL för att testa tjänsten från den virtuella Notebook-datorn. Om du vill testa från en fjärran sluten klient hämtar du den offentliga URL: en för tjänsten som körs på den virtuella Notebook-datorn. Den offentliga URL: en kan fastställas med hjälp av följande formel. `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Till exempel `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

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