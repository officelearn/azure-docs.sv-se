---
title: Kör Azure Container instances
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren LUIS till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 6a714178c9f8ec3f9239ba64bccbcf25b4aecf60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465928"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuera Language Understanding-behållaren (LUIS) till Azure Container instances

Lär dig hur du distribuerar Cognitive Services [Luis](luis-container-howto.md) -behållaren till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar hur du skapar en avvikelse detektor resurs. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
