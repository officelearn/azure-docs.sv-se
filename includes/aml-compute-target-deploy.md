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
ms.openlocfilehash: 05082544830bad543d6743d016a91ef8565767f4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331656"
---
| Beräkningsmål | Användning | GPU-stöd | Stöd för FPGA | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokala&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testa/debug | maybe | &nbsp; | Bra för begränsad testning och felsökning. Maskinvaruacceleration beror på med hjälp av bibliotek i det lokala systemet.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | I realtid inferens |  [Ja](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Ja](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Bra för Produktionsdistribution av hög skalbarhet. Ger snabb svarstid och automatisk skalning av den distribuerade tjänsten. Klustret autoskalning stöds inte via SDK: N för Azure Machine Learning. Du kan ändra noderna i AKS-kluster med Användargränssnittet för AKS-kluster i Azure-portalen. AKS är det enda alternativet som är tillgängliga för det visuella gränssnittet. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testning eller utveckling | &nbsp;  | &nbsp; | Bra för CPU-baserade arbetsbelastningar som kräver låg skala < 48 GB RAM-minne |
| [Azure Machine Learning-beräkning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Förhandsversion) Batch&nbsp;inferens | ja | &nbsp;  | Kör batchbedömnings på beräkning utan server. Stöder normalt och lågprioriterade virtuella datorer. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Förhandsversion) IoT&nbsp;modul |  &nbsp; | &nbsp; | Distribuera och hantera ML-modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | ja | Distribuera och hantera ML-modeller på IoT-enheter. |
