---
title: Kör ansiktsbehållare i Azure Container-instanser
titleSuffix: Azure Cognitive Services
description: Distribuera Ansiktsbehållaren till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716258"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Distribuera ansiktsbehållaren till Azure Container Instances

Lär dig hur du [Face](../face-how-to-install-containers.md) distribuerar Cognitive Services Face-behållaren till Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av en Azure Face-resurs. Sedan diskuterar vi att dra den tillhörande behållarbilden. Slutligen belyser vi möjligheten att utöva orkestrering av de två från en webbläsare. Med hjälp av behållare kan flytta utvecklarnas uppmärksamhet från att hantera infrastruktur till att istället fokusera på applikationsutveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till det privata behållarregistret

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]