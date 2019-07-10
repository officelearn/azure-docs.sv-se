---
title: Kör Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren för visuellt innehåll till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711619"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Distribuera behållaren för visuellt innehåll till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [visuellt](computer-vision-how-to-install-containers.md) behållare till Azure [Behållarinstanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av resursen för visuellt innehåll. Därefter ska vi prata hämta associerade behållaravbildningen. Slutligen kan markerar vi du möjlighet att utöva dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]