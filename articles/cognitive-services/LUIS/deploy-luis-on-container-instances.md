---
title: Kör Azure Container Instances
titleSuffix: Azure Cognitive Services
description: LUIS-behållaren distribueras till en Azure-Behållarinstans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711575"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuera behållaren Språkförståelse (LUIS) till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [LUIS](luis-container-howto.md) behållare till Azure [Behållarinstanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av en Avvikelseidentifiering detektor-resurs. Därefter ska vi prata hämta associerade behållaravbildningen. Slutligen kan markerar vi du möjlighet att utöva dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
