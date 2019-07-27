---
title: Köra Azure Kubernetes service – Textanalys
titleSuffix: Azure Cognitive Services
description: Distribuera Textanalys-behållare med sentiment Analysis-avbildningen till Azure Kubernetes-tjänsten och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552277"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Distribuera en sentiment Analysis-behållare till Azure Kubernetes-tjänsten

Lär dig hur du distribuerar Azure Cognitive Services [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) -behållaren med sentiment Analysis-avbildningen till Azure Kubernetes service (AKS). Den här proceduren visar hur du skapar en Textanalys resurs, hur du skapar en associerad sentiment-analys avbildning och hur du använder den här dirigeringen av de två från en webbläsare. Genom att använda behållare kan du flytta uppmärksamheten från att hantera infrastrukturen i stället för att fokusera på program utveckling.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud Shell. Du behöver följande:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En text redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installerat.
* [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) har installerats.
* En Azure-resurs med rätt pris nivå. Alla pris nivåer fungerar inte med den här behållaren:
    * Endast **Azure textanalys** -resurser med F0 eller standard pris nivåer.
    * **Azure Cognitive Services** -resurs med pris nivån S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Distribuera en Textanalys-behållare till ett AKS-kluster

1. Öppna Azure CLI och logga in på Azure.

    ```azurecli
    az login
    ```

1. Logga in på AKS-klustret. Ersätt `your-cluster-name` och`your-resource-group` med lämpliga värden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    När det här kommandot körs rapporterar det ett meddelande som liknar följande:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Om du har flera prenumerationer tillgängliga på ditt Azure-konto och `az aks get-credentials` kommandot returnerar med ett fel, är det ett vanligt problem att du använder fel prenumeration. Ställ in kontexten för Azure CLI-sessionen så att den använder samma prenumeration som du skapade resurserna med och försök igen.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öppna valfri text redigerare. I det här exemplet används Visual Studio Code.

    ```azurecli
    code .
    ```

1. I text redigeraren skapar du en ny fil med namnet _sentiment. yaml_och klistrar in följande yaml i den. Se till att ersätta `billing/value` och `apikey/value` med din egen information.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Spara filen och Stäng text redigeraren.
1. Kör kommandot Kubernetes `apply` med _sentiment. yaml_ som mål:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    När kommandot har tillämpat distributions konfigurationen visas ett meddelande som liknar följande utdata:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Kontrol lera att Pod har distribuerats:

    ```console
    kubectl get pods
    ```

    Utdata för körnings status för pod:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Kontrol lera att tjänsten är tillgänglig och hämta IP-adressen.

    ```console
    kubectl get services
    ```

    Utdata för körnings statusen för _sentiment_ -tjänsten i pod:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Nästa steg

* Använd fler [Cognitive Services behållare](../../cognitive-services-container-support.md)
* Använd [textanalys anslutna tjänsten](../vs-text-connected-service.md)
