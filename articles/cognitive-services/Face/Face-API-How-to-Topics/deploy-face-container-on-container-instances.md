---
title: Kör ansikts behållare i Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera ansikts behållaren till en Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: a5354581be519172c498e57d25510f9fc5c0daa4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911264"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Distribuera ansikts behållaren till Azure Container Instances

> [!IMPORTANT]
> Gränsen för antal användare av Ansiktsigenkänningscontainern har nåtts. Vi tar för närvarade inte emot fler ansökningar till Ansiktsigenkänningscontainern.

Lär dig hur du distribuerar behållaren Cognitive Services [ansikte](../face-how-to-install-containers.md) till Azure [container instances](../../../container-instances/index.yml). Den här proceduren visar hur du skapar en Azure-ansikts resurs. Sedan diskuterar vi hämtningen av den tillhör ande behållar avbildningen. Slutligen fokuserar vi på att kunna utnyttja dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]