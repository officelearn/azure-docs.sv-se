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
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753096"
---
| Beräkningsmål | Användning | GPU-stöd | Stöd för FPGA | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokala webbtjänst](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testa/debug | maybe | &nbsp; | Bra för begränsad testning och felsökning. Maskinvaruacceleration beror på med hjälp av bibliotek i det lokala systemet.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | I realtid inferens |  [Ja](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Ja](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Bra för Produktionsdistribution av hög skalbarhet. Tillhandahåller automatisk skalning och snabba svarstider.  AKS är det enda alternativet som är tillgängliga för det visuella gränssnittet. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testning eller utveckling | &nbsp;  | &nbsp; | Bra för CPU-baserade arbetsbelastningar som kräver låg skala < 48 GB RAM-minne |
| [Azure Machine Learning-beräkning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Förhandsversion) Batch inferens | ja | &nbsp;  | Kör batchbedömnings på beräkning utan server. Stöder normalt och lågprioriterade virtuella datorer. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Förhandsversion) IoT-modul |  &nbsp; | &nbsp; | Distribuera och hantera ML-modeller på IoT-enheter. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | ja | Distribuera och hantera ML-modeller på IoT-enheter. |