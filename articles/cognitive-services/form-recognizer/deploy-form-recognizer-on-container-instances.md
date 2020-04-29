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
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879538"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuera formulär identifierarens behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [formulärets igenkännings](form-recognizer-container-howto.md) behållare till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar hur du skapar en identifierings resurs för Azure-formulär. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

> [!IMPORTANT]
> Formulär tolknings behållarna använder för närvarande version 1,0 av formulärets tolknings-API. Du kan komma åt den senaste versionen av API: et genom att använda den hanterade tjänsten i stället.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Du måste först fylla i och skicka in [formuläret för formulär för Cognitive Services som identifierar behållar formuläret](https://aka.ms/FormRecognizerContainerRequestAccess) för att begära åtkomst till behållaren. När du gör det loggas du också in för Visuellt innehåll. Du behöver inte registrera dig för formuläret för Visuellt innehåll begäran separat. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
