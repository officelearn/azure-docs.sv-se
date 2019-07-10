---
title: Kör Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuera text analytics-behållare med sentiment analysis-avbildning till Azure-Behållarinstans och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9f174d54fcc74eed613eb69412bc0e515f15897b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711729"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Distribuera en Attitydanalys behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) behållare med Attitydanalys-avbildning till Azure [Behållarinstanser](https://docs.microsoft.com/azure/container-instances/). Den här proceduren är ett exempel på att skapa en resurs för textanalys, att skapa en tillhörande Attitydanalys-avbildningen och möjlighet att arbeta med den här dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

## <a name="prerequisites"></a>Förutsättningar

* Använda en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Nästa steg 

* Använder mer [Cognitive Services-behållare](../../cognitive-services-container-support.md)
* Använd den [textanalys ansluten tjänst](../vs-text-connected-service.md)
