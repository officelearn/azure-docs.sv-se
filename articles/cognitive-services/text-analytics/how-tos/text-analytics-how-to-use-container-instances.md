---
title: Kör Azure Container Instances-Textanalys
titleSuffix: Azure Cognitive Services
description: Distribuera text Analytics-behållare till Azure Container instance och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e4b61c6fe2f62745d0f5268221cbb5c84803eb10
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876477"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Distribuera en Textanalys-behållare till Azure Container Instances

Lär dig hur du distribuerar Cognitive Services [textanalys][install-and-run-containers] -behållaren till Azure [container instances][container-instances]. Den här proceduren exemplifies skapandet av en Textanalys resurs, skapandet av en associerad Attitydanalys avbildning och möjlighet att utnyttja den här dirigeringen av de två från en webbläsare. Genom att använda behållare kan du byta utvecklares uppmärksamhet från att hantera infrastrukturen i stället för att fokusera på program utveckling.

## <a name="prerequisites"></a>Krav

* Använd en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extrahering av nyckelfraser](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Nästa steg 

* Använd fler [Cognitive Services behållare](../../cognitive-services-container-support.md)
* Använd [textanalys anslutna tjänsten](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances