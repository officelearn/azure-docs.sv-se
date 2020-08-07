---
title: Hur och var modeller ska distribueras
titleSuffix: Azure Machine Learning
description: Lär dig hur och var du kan distribuera Azure Machine Learning-modeller, inklusive Azure Container Instances, Azure Kubernetes service, Azure IoT Edge och fält-programmerbara grind mat ris.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 5e73744f3d467d08944d0e8800dd6d8824857abd
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846997"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuera modeller med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar din Machine Learning-modell som en webb tjänst i Azure-molnet eller Azure IoT Edge enheter.

Arbetsflödet är ungefär likadant var du än distribuerar din modell:

1. Registrera modellen.
1. Förbered en konfiguration för en härledning
1. Förbereda ett Entry-skript (om du inte använder [distribution utan kod](how-to-deploy-no-code-deployment.md))
1. Distribuera modellen till beräkningsmålet.
1. Testa den distribuerade modellen, även kallad en webb tjänst.

Mer information om de begrepp som ingår i distributions arbets flödet finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning](concept-model-management-and-deployment.md).


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>Förstå tjänst tillstånd

Under modell distributionen kan tjänst tillstånds ändringen visas när den distribueras fullständigt.

I följande tabell beskrivs de olika tjänst tillstånden:

| Webservice-tillstånd | Beskrivning | Slutligt tillstånd?
| ----- | ----- | ----- |
| Övergår | Tjänsten håller på att distribueras. | Nej |
| Ohälsosamt | Tjänsten har distribuerats men är för närvarande inte tillgänglig.  | Nej |
| Unschedulable | Det går inte att distribuera tjänsten för tillfället på grund av bristande resurser. | Nej |
| Misslyckad | Det gick inte att distribuera tjänsten på grund av ett fel eller en krasch. | Ja |
| Felfri | Tjänsten är felfri och slut punkten är tillgänglig. | Ja |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Batch-härledning
Azure Machine Learning beräknings mål skapas och hanteras av Azure Machine Learning. De kan användas för batch förutsägelse från Azure Machine Learning pipeliner.

En genom gång av batch-härledning med Azure Machine Learning Compute finns i [så här kör du batch-förutsägelser](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge-härledning
Stöd för att distribuera till Edge är i för hands version. Mer information finns i [distribuera Azure Machine Learning som en IoT Edge modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använd TLS för att skydda en webb tjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)

