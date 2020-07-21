---
title: Kör formulär igenkännings behållare i Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera formulärets tolknings behållare till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: de81fcb5ee62f9b295c93132b271507c040af46a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512798"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuera formulär identifierarens behållare till Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Lär dig hur du distribuerar Cognitive Services [formulärets igenkännings](form-recognizer-container-howto.md) behållare till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar hur du skapar en identifierings resurs för Azure-formulär. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
