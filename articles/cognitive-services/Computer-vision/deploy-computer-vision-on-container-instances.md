---
title: Kör Visuellt innehåll behållare i Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera Visuellt innehåll-behållaren till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876109"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Distribuera Visuellt innehåll-behållaren till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [visuellt innehåll](computer-vision-how-to-install-containers.md) -behållaren till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar att Visuellt innehåll-resursen skapas. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till registret för privat behållare

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]