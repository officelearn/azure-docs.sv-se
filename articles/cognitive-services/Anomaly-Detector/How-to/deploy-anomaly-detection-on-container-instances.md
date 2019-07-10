---
title: Kör Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Avvikelseidentifiering detektor behållaren distribueras till en Azure-Behållarinstans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711696"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuera en Avvikelseidentifiering detektor-behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [Avvikelseidentifiering detektor](../anomaly-detector-container-howto.md) behållare till Azure [Behållarinstanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av en Avvikelseidentifiering detektor-resurs. Därefter ska vi prata hämta associerade behållaravbildningen. Slutligen kan markerar vi du möjlighet att utöva dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

Du måste först slutför och skicka den [formulär för Avvikelseidentifiering detektor behållare](https://aka.ms/adcontainer) att begära åtkomst till behållaren.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]