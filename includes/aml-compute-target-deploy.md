---
title: inkludera fil
description: inkludera fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/25/2020
ms.openlocfilehash: bd3ac8d512c1b9d151c0dc549ffeee6a05c7f94b
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542827"
---
Det beräknings mål som du använder för att vara värd för din modell påverkar kostnaden och tillgängligheten för den distribuerade slut punkten. Använd tabellen nedan för att välja ett lämpligt beräknings mål.

| Beräkningsmål | Används för | GPU-stöd | FPGA-stöd | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokal &nbsp; webb &nbsp; tjänst](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning. Maskin varu accelerationen beror på användningen av bibliotek i det lokala systemet.
| [Azure Machine Learning Compute instance &nbsp; - &nbsp; webbtjänst](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Real tids härledning |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webb tjänst distribution) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Använd för storskaliga produktions distributioner. Ger snabb svars tid och automatisk skalning av den distribuerade tjänsten. Automatisk skalning i kluster stöds inte via Azure Machine Learning SDK. Om du vill ändra noderna i AKS-klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. AKS är det enda tillgängliga alternativet för designern. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testning eller utveckling | &nbsp;  | &nbsp; | Använd för processorbaserade CPU-baserade arbets belastningar som kräver mindre än 48 GB RAM-minne. |
| [Azure Machine Learning-beräkningskluster](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch- &nbsp; härledning | [Ja](../articles/machine-learning/how-to-use-parallel-run-step.md) (Machine Learning-pipeline) | &nbsp;  | Kör batch-poängsättning vid Server lös beräkning. Stöder normal och låg prioritet för virtuella datorer. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Förhandsgranskningsvyn Real tids härledning | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Förhandsgranskningsvyn IoT- &nbsp; modul |  &nbsp; | &nbsp; | Distribuera och hantera ML-modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Via IoT Edge |  &nbsp; | Yes | Distribuera och hantera ML-modeller på IoT-enheter. |

> [!NOTE]
> Även om Compute-mål som lokala, Azure Machine Learning beräknings instans och Azure Machine Learning beräknings kluster stöder GPU för utbildning och experimentering, kan du använda GPU: n för __en härledning när den distribueras som en webb tjänst__ endast stöds i Azure Kubernetes-tjänsten.
>
> Användning av en GPU för härledning __när poäng med en Machine Learning-pipeline__ stöds endast på Azure Machine Learning Compute.

> [!NOTE]
> * Azure Container Instances (ACI) passar bara för små modeller som är mindre än 1 GB i storlek. 
> * Vi rekommenderar att du använder AKS-kluster (Single-Node Azure Kubernetes service) för utveckling och testning av större modeller.