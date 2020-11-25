---
title: Köra Azure Kubernetes service – Textanalys
titleSuffix: Azure Cognitive Services
description: Distribuera Textanalys behållar avbildningen till Azure Kubernetes-tjänsten och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 744be742d9f638df14f9d6720c9dad9678685af0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017895"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Distribuera en Textanalys-behållare till Azure Kubernetes-tjänsten

Lär dig hur du distribuerar Azure Cognitive Services [textanalys](./text-analytics-how-to-install-containers.md) container image till Azure Kubernetes service (AKS). Den här proceduren visar hur du skapar en Textanalys resurs, hur du skapar en associerad sentiment-analys avbildning och hur du använder den här dirigeringen av de två från en webbläsare. Genom att använda behållare kan du flytta uppmärksamheten från att hantera infrastrukturen i stället för att fokusera på program utveckling.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud Shell. Du behöver följande:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services) innan du börjar.
* En text redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) installerat.
* [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) har installerats.
* En Azure-resurs med rätt pris nivå. Alla pris nivåer fungerar inte med den här behållaren:
    * Endast **Azure textanalys** -resurser med F0 eller standard pris nivåer.
    * **Azure Cognitive Services** -resurs med pris nivån S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

**_

## <a name="next-steps"></a>Nästa steg

_ Använd fler [Cognitive Services behållare](../../cognitive-services-container-support.md)
* Använd [textanalys anslutna tjänsten](../index.yml)