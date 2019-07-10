---
title: Kör Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Formuläret Igenkännande behållaren distribueras till en Azure-Behållarinstans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711399"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuera formuläret Igenkännande behållaren till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [formuläret Igenkännande](form-recognizer-container-howto.md) behållare till Azure [Behållarinstanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av en Azure formuläret Igenkännande-resurs. Därefter ska vi prata hämta associerade behållaravbildningen. Slutligen kan markerar vi du möjlighet att utöva dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först slutför och skicka den [Cognitive Services formuläret Igenkännande behållare åtkomst till formulär för](https://aka.ms/FormRecognizerRequestAccess) att begära åtkomst till behållaren. Gör det också loggar du in för visuellt innehåll. Du behöver inte registrera dig för formuläret visuellt separat. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
