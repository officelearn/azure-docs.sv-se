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
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913178"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuera formulär identifierarens behållare till Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Lär dig hur du distribuerar Cognitive Services [formulärets igenkännings](form-recognizer-container-howto.md) behållare till Azure [container instances](../../container-instances/index.yml). Den här proceduren visar hur du skapar en identifierings resurs för Azure-formulär. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]