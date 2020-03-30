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
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122599"
---
| Beräkningsmål | Används för | STÖD för GPU | Support för FPGA | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokal&nbsp;&nbsp;webbtjänst](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testa/felsöka | &nbsp; | &nbsp; | Används för begränsad testning och felsökning. Maskinvaruacceleration beror på användningen av bibliotek i det lokala systemet.
| [Azure Machine Learning-beräkningsinstanswebbtjänst&nbsp;&nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testa/felsöka | &nbsp; | &nbsp; | Används för begränsad testning och felsökning.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferens i realtid |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (distribution av webbtjänster) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Används för storskaliga produktionsdistributioner. Ger snabb svarstid och automatisk skalning av den distribuerade tjänsten. Automatisk automatisk skalning av kluster stöds inte via Azure Machine Learning SDK. Om du vill ändra noderna i AKS-klustret använder du användargränssnittet för AKS-klustret i Azure-portalen. AKS är det enda tillgängliga alternativet för designern. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testning eller utveckling | &nbsp;  | &nbsp; | Används för processorbaserade arbetsbelastningar i låg skala som kräver mindre än 48 GB RAM.Use for low-scale CPU-based workloads that require less than 48 GB OF RAM. |
| [Azure Machine Learning-beräkningskluster](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Förhandsgranska) Slutsatser&nbsp;om batch | [Ja](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline för maskininlärning) | &nbsp;  | Kör batchbedömning på serverlös beräkning. Stöder normala och lågprioriterade virtuella datorer. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Förhandsgranska) Inferens i realtid | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Förhandsgranska) IoT-modul&nbsp; |  &nbsp; | &nbsp; | Distribuera och betjäna ML-modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | Distribuera och betjäna ML-modeller på IoT-enheter. |

> [!NOTE]
> Även om beräkningsmål som lokala, Azure Machine Learning-beräkningsinstanser och Azure Machine Learning-beräkningskluster stöder GPU för utbildning och experiment, stöds GPU för slutledning __när de distribueras som en webbtjänst__ endast på Azure Kubernetes-tjänsten.
>
> Att använda en GPU för slutledning __när du gör mål med en machine learning pipeline__ stöds endast på Azure Machine Learning Compute.