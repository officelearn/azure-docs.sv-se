---
title: Kör Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Ansikts-behållaren distribueras till en Azure-Behållarinstans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: e67846b6b304b5425f7e8334eb3a4499a029d5ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711597"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Distribuera Ansikts-behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [ansikte](../face-how-to-install-containers.md) behållare till Azure [Behållarinstanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av ett ansikte i Azure-resurs. Därefter ska vi prata hämta associerade behållaravbildningen. Slutligen kan markerar vi du möjlighet att utöva dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]