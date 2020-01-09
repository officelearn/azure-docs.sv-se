---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 59e545e788fd6173de70e6d1580cf2832f71b72b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540469"
---
| Beräkningsmål | Används för | GPU-stöd | FPGA-stöd | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokal&nbsp;Web&nbsp;-tjänsten](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning. Maskin varu accelerationen beror på användningen av bibliotek i det lokala systemet.
| [Azure Machine Learning beräknings instans&nbsp;webb&nbsp;s tjänsten](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Real tids härledning |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webb tjänst distribution) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Använd för storskaliga produktions distributioner. Ger snabb svars tid och automatisk skalning av den distribuerade tjänsten. Automatisk skalning i kluster stöds inte via Azure Machine Learning SDK. Om du vill ändra noderna i AKS-klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. AKS är det enda tillgängliga alternativet för designern. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testning eller utveckling | &nbsp;  | &nbsp; | Använd för processorbaserade CPU-baserade arbets belastningar som kräver mindre än 48 GB RAM-minne. |
| [Azure Machine Learning beräknings kluster](../articles/machine-learning/how-to-run-batch-predictions.md) | Förhandsgranskningsvyn Batch&nbsp;-härledning | [Ja](../articles/machine-learning/how-to-run-batch-predictions.md) (Machine Learning-pipeline) | &nbsp;  | Kör batch-poängsättning vid Server lös beräkning. Stöder normal och låg prioritet för virtuella datorer. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Förhandsgranskningsvyn Real tids härledning | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Förhandsgranskningsvyn IoT&nbsp;-modul |  &nbsp; | &nbsp; | Distribuera och hantera ML-modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | Distribuera och hantera ML-modeller på IoT-enheter. |

> [!NOTE]
> Även om Compute-mål som lokala, Azure Machine Learning beräknings instans och Azure Machine Learning beräknings kluster stöder GPU för utbildning och experimentering, kan du använda GPU: n för __en härledning när den distribueras som en webb tjänst__ endast stöds i Azure Kubernetes-tjänsten.
>
> Användning av en GPU för härledning __när poäng med en Machine Learning-pipeline__ stöds endast på Azure Machine Learning Compute.