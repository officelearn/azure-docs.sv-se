---
title: Kör Azure Container Instances-Textanalys
titleSuffix: Azure Cognitive Services
description: Distribuera text Analytics-behållare med sentiment-analys avbildningen till Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552529"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Distribuera en Attitydanalys-behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) -behållaren med Attitydanalys-avbildningen till Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren exemplifies skapandet av en Textanalys resurs, skapandet av en associerad Attitydanalys avbildning och möjlighet att utnyttja den här dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

## <a name="prerequisites"></a>Förutsättningar

* Använd en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Nästa steg 

* Använd fler [Cognitive Services behållare](../../cognitive-services-container-support.md)
* Använd [textanalys anslutna tjänsten](../vs-text-connected-service.md)
