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
ms.openlocfilehash: 944b96e7726f2b2becd5960a17a89c00d00c878a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841974"
---
Det beräknings mål som du använder för att vara värd för din modell påverkar kostnaden och tillgängligheten för den distribuerade slut punkten. Använd den här tabellen för att välja ett lämpligt beräknings mål.

| Beräkningsmål | Används för | GPU-stöd | FPGA-stöd | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokal &nbsp; webb &nbsp; tjänst](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning. Maskin varu accelerationen beror på användningen av bibliotek i det lokala systemet.
| [Azure Machine Learning Compute instance &nbsp; - &nbsp; webbtjänst](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Real tids härledning |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webb tjänst distribution) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Använd för storskaliga produktions distributioner. Ger snabb svars tid och automatisk skalning av den distribuerade tjänsten. Automatisk skalning i kluster stöds inte via Azure Machine Learning SDK. Om du vill ändra noderna i AKS-klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. AKS är det enda tillgängliga alternativet för designern. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testning eller utveckling | &nbsp;  | &nbsp; | Använd för processorbaserade CPU-baserade arbets belastningar som kräver mindre än 48 GB RAM-minne. |
| [Azure Machine Learning-beräkningskluster](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch- &nbsp; härledning | [Ja](../articles/machine-learning/how-to-use-parallel-run-step.md) (Machine Learning-pipeline) | &nbsp;  | Kör batch-poängsättning vid Server lös beräkning. Stöder normal och låg prioritet för virtuella datorer. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Förhandsgranskningsvyn Real tids härledning | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Förhandsgranskningsvyn IoT- &nbsp; modul |  &nbsp; | &nbsp; | Distribuera och hantera maskin inlärnings modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | Distribuera och hantera maskin inlärnings modeller på IoT-enheter. |

> [!NOTE]
> Även om Compute-mål som Local, Azure Machine Learning Compute och Azure Machine Learning beräknings kluster stöder GPU för utbildning och experimentering, kan du använda GPU för _en härledning när den distribueras som en webb tjänst_ bara stöds på AKS.
>
> Användning av en GPU för härledning _när poäng med en Machine Learning-pipeline_ stöds endast på Azure Machine Learning Compute.

> [!NOTE]
> * Container instances lämpar sig bara för små modeller som är mindre än 1 GB stora.
> * Använd AKS-kluster med en nod för utveckling och testning av större modeller.