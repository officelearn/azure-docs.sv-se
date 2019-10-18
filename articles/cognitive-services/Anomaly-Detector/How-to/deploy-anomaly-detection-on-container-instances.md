---
title: Kör avvikelse detektor behållare i Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera avvikelse detektor behållaren till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d2e3ed59a7ad71177f34909e37ce885d603297
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515267"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuera en avvikelse detektor behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [avvikelse detektor](../anomaly-detector-container-howto.md) behållare till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar hur du skapar en avvikelse detektor resurs. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till registret för privat behållare

Du måste först fylla i och skicka in ett [formulär för avvikelse detektor container](https://aka.ms/adcontainer) för att begära åtkomst till behållaren.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nästa steg

* Granska [installations-och körnings behållare](../anomaly-detector-container-configuration.md) för att hämta behållar avbildningen och köra behållaren
* Granska [Konfigurera behållare](../anomaly-detector-container-configuration.md) för konfigurations inställningar
* [Läs mer om API-tjänsten för avvikelse detektor](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
