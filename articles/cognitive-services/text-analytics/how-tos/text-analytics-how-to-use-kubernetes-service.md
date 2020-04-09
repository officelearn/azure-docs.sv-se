---
title: Kör Azure Kubernetes-tjänsten – textanalys
titleSuffix: Azure Cognitive Services
description: Distribuera behållaravbildningen för Text Analytics till Azure Kubernetes Service och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877868"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Distribuera en Text Analytics-behållare till Azure Kubernetes Service

Lär dig hur du distribuerar behållaren För Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) till Azure Kubernetes Service (AKS). Den här proceduren visar hur du skapar en Text Analytics-resurs, hur du skapar en associerad sentimentanalysbild och hur du tränar den här orkestreringen av de två från en webbläsare. Använda behållare kan flytta din uppmärksamhet från att hantera infrastruktur till istället fokusera på applikationsutveckling.

## <a name="prerequisites"></a>Krav

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud Shell. Du behöver följande:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* En textredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) har installerats.
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installerat.
* En Azure-resurs med rätt prisnivå. Alla prisnivåer fungerar inte med den här behållaren:
    * **Azure Text Analytics-resurs** med endast F0- eller standardprisnivåer.
    * **Azure Cognitive Services-resurs** med prisnivån S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extrahering av nyckelfraser](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Nästa steg

* Använda fler [Cognitive Services-behållare](../../cognitive-services-container-support.md)
* Använda tjänsten [Text Analytics Connected](../vs-text-connected-service.md)
