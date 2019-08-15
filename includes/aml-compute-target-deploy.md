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
ms.date: 05/30/2019
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946049"
---
| Beräkningsmål | Användning | GPU-stöd | FPGA-stöd | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokal&nbsp;webb&nbsp;tjänst](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testning/fel sökning | kanske | &nbsp; | Lämpar sig för begränsad testning och fel sökning. Maskin varu accelerationen är beroende av att använda bibliotek i det lokala systemet.
| [&nbsp;Webb&nbsp;tjänst för notebook VM](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testning/fel sökning | kanske | &nbsp; | Lämpar sig för begränsad testning och fel sökning. 
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Real tids härledning |  [Ja](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Ja](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Bra för Produktionsdistribution av hög skalbarhet. Ger snabb svars tid och automatisk skalning av den distribuerade tjänsten. Automatisk skalning i kluster stöds inte via Azure Machine Learning SDK. Om du vill ändra noderna i AKS-klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. AKS är det enda alternativet som är tillgängligt för det visuella gränssnittet. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testning eller utveckling | &nbsp;  | &nbsp; | Lämpat för låg skalning, CPU-baserade arbets belastningar som kräver < 48 GB RAM-minne |
| [Azure Machine Learning-beräkning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Förhandsgranskningsvyn Batch&nbsp;-härledning | ja | &nbsp;  | Kör batch-poängsättning vid Server lös beräkning. Stöder normal och låg prioritet för virtuella datorer. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Förhandsgranskningsvyn IoT&nbsp;-modul |  &nbsp; | &nbsp; | Distribuera & tjänar ML-modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | ja | Distribuera & tjänar ML-modeller på IoT-enheter. |
