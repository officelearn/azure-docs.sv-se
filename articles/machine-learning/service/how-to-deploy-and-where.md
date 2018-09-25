---
title: Var du vill distribuera modeller med Azure Machine Learning-tjänsten | Microsoft Docs
description: Läs mer om hur du kan distribuera dina modeller i produktion med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961016"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuera modeller med Azure Machine Learning-tjänsten

Azure Machine Learning-tjänsten finns flera sätt du kan distribuera den tränade modellen. I det här dokumentet lär du dig hur du distribuerar din modell som en webbtjänst i Azure-molnet, eller IoT edge-enheter.

Du kan distribuera modeller till följande beräkning:

- Azure Container Instances (ACI)
- Azure Kubernetes Service (AKS)
- Azure IoT Edge
- Fältet-programmable gate array FPGA)

Resten av det här dokumentet pratar om var och en av dessa alternativ i detalj.

## <a name="azure-container-instances"></a>Azure Container Instances

Använd Azure Container Instances för att distribuera dina modeller som en REST API-slutpunkt om en eller flera av följande villkor är uppfyllt:
- Du letar efter en snabb distribution för att kunna bedöma och verifiera din modell. ACI-distributionen är vanligtvis klar på mindre än 5 minuter.
- Du vill distribuera din modell i en utvecklings- eller testmiljö. ACI kan du distribuera 20 behållargrupper per prenumeration. Mer information finns i den [kvoter och regiontillgänglighet för Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentet.

Mer information finns i den [distribuera en modell till Azure Container Instances](how-to-deploy-to-aci.md) dokumentet.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Scenarier med hög skalbarhet produktion kan du distribuera din modell till Azure Kubernetes Service (AKS). Du kan använda ett befintligt AKS-kluster eller skapa en ny med SDK: N för Azure Machine Learning, CLI eller Azure-portalen.

Skapa AKS-klustret är en tid processen för arbetsytan. När du skapat kan du återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resursgruppen som innehåller den, måste du skapa ett nytt kluster nästa gång du behöver distribuera.

Distribuera till AKS tillhandahåller automatisk skalning, loggning, insamling av modelldata och snabba svarstider för dina webbtjänster. 

Processen för att skapa ett AKS-kluster tar cirka 20 minuter.

Mer information finns i den [distribuera en modell till Azure Kubernetes Service](how-to-deploy-to-aks.md) dokumentet.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Med IoT-enheter är det snabbare att utföra bedömning på enheten i stället för att skicka data till molnet och väntar på en molnbaserad modell att returnera data. Du kan ha din modell på edge-enheter med Azure IoT Edge. Distribuera din modell till IoT Edge om du behöver en eller flera av följande funktioner:
- Hantera prioritet aktiviteter lokalt, även om du inte en cloud-anslutning
- Arbeta med genererade data som är för stor för att hämta snabbt från molnet
- Aktivera bearbetning i realtid via intelligens i eller nära lokala enheter
- Hantera sekretess-relaterade krav 

Mer information finns i den [distribuera till Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) dokumentet.

Mer information om IoT Edge-tjänsten finns i den [dokumentation om Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a name="field-programmable-gate-arrays-fpga"></a>Fältet-programmable gate matriser (FPGA)

Maskinvara Accelerated modeller som drivs av Project Brainwave gör det möjligt att uppnå extremt låg latens för i realtid inferensjobb begäranden. Project Brainwave går det snabbare djupa neurala nätverk (DNN) som har distribuerats på fält-programmable gate matriser i Azure-molnet. Vanligaste dnn: er är tillgängliga som featurizers för induktiv inlärning eller anpassningsbara med vikterna tränas från dina egna data.

Mer information finns i den [distribuera till en FPGA](how-to-deploy-fpga-web-service.md) dokumentet.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du distribuerar en modell till en specifik beräkningsmål finns i följande dokument:

* [Distribuera en modell till Azure Container Instances](how-to-deploy-to-aci.md)
* [Distribuera en modell till Azure Kubernetes Service](how-to-deploy-to-aks.md)
* [Distribuera en modell till Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Distribuera en modell till FPGA](how-to-deploy-fpga-web-service.md)
