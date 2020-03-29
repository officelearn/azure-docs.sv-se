---
title: Köra behållare för datorseende i Azure Container-instanser
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren för datorseende till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1c858432a3382e6dbc8e479aab11b18dc5eebfe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499095"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Distribuera behållaren för datorseende till Azure Container-instanser

Lär dig hur du distribuerar behållaren för Cognitive Services [Computer Vision](computer-vision-how-to-install-containers.md) till Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av datorns visionsresurs. Sedan diskuterar vi att dra den tillhörande behållarbilden. Slutligen belyser vi möjligheten att utöva orkestrering av de två från en webbläsare. Med hjälp av behållare kan flytta utvecklarnas uppmärksamhet från att hantera infrastruktur till att istället fokusera på applikationsutveckling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till det privata behållarregistret

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]