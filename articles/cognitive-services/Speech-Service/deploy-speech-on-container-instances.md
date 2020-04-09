---
title: Kör Azure Container Instances - Taltjänst
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren för taltjänsten till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 610d5ce095097a31ee92c67f0112d1657424858e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878756"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Distribuera behållaren för taltjänst till Azure Container Instances

Lär dig hur du distribuerar behållaren för Cognitive Services [Speech-tjänsten](speech-container-howto.md) till Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av en Azure Speech-tjänstresurs. Sedan diskuterar vi att dra den tillhörande behållarbilden. Slutligen belyser vi möjligheten att utöva orkestrering av de två från en webbläsare. Med hjälp av behållare kan flytta utvecklarnas uppmärksamhet från att hantera infrastruktur till att istället fokusera på applikationsutveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först fylla i och skicka formuläret Begäran om begäran om begäran om åtkomst till behållaren för [kognitiva tjänster.](https://aka.ms/speechcontainerspreview/) 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
