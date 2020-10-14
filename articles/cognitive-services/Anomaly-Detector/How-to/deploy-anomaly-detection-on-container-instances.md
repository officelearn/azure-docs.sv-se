---
title: Kör avvikelse detektor behållare i Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera avvikelse detektor behållaren till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: c7ce1985f4c7dcd14befce14abe5b913e2c9a67e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014756"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuera en avvikelse detektor behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [avvikelse detektor](../anomaly-detector-container-howto.md) behållare till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar hur du skapar en avvikelse detektor resurs. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nästa steg

* Granska [installations-och körnings behållare](../anomaly-detector-container-configuration.md) för att hämta behållar avbildningen och köra behållaren
* Granska [Konfigurera behållare](../anomaly-detector-container-configuration.md) för konfigurations inställningar
* [Läs mer om API-tjänsten för avvikelse detektor](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
