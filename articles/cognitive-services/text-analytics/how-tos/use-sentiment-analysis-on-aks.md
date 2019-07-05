---
title: Kör Azure Kubernetes-tjänster
titleSuffix: Text Analytics - Azure Cognitive Services
description: Distribuera text analytics-behållare med sentiment analysis-avbildning till Azure Kubernetes-tjänster och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a419ed3b9c0d2c4db9c552642dc5c662786f6730
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561258"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Distribuera en Attitydanalys-behållare till Azure Kubernetes Services (AKS)

Lär dig hur du distribuerar Cognitive Services [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) behållare med Attitydanalys avbildning till Azure Kubernetes Services (AKS). Den här proceduren är ett exempel på att skapa en resurs för textanalys, att skapa en tillhörande Attitydanalys-avbildningen och möjlighet att arbeta med den här dirigering av två från en webbläsare. Med hjälp av behållare kan du ändra den utvecklare uppmärksamhet från hantering av infrastruktur för att i stället fokusera på programutveckling.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud shell.

* Använda en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Textredigerare, till exempel: [Visual Studio Code](https://code.visualstudio.com/download).
* Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Installera den [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* En Azure-resurs med rätt prisnivån. Inte alla prisnivåer arbetar du med den här behållaren:
    * **Textanalys** resurs med F0 eller standardpriserna nivåerna endast.
    * **Cognitive Services** resurs med S0 prisnivå.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Text Analytics behållaren distribueras till ett AKS-kluster

1. Öppna Azure CLI och logga in till Azure

    ```azurecli
    az login
    ```

1. Logga in på AKS-klustret (Ersätt den `your-cluster-name` och `your-resource-group` med lämpliga värden)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    När det här kommandot körs, rapporterar den ett meddelande som liknar följande:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Om du har flera prenumerationer som är tillgängliga på ditt Azure-konto och `az aks get-credentials` kommandot returnerar med ett fel, ett vanligt problem är att du använder fel prenumeration. Helt enkelt ange sedan kontexten för att använda samma prenumeration som du skapade resurser med Azure CLI-sessionen och försök igen.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öppna valfri, textredigerare (det här exemplet används __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. Skapa en ny fil med namnet i textredigeraren _sentiment.yaml_ och klistra in följande YAML till den. Se till att ersätta den `billing/value` och `apikey/value` med dina egna.

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

1. Spara filen och stäng textredigeraren.
1. Kör Kubernetes `apply` med den _sentiment.yaml_ som dess mål:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    När kommandot har tillämpats distributionskonfiguration, ett meddelande som liknar följande utdata:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Kontrollera att din POD har distribuerats:

    ```console
    kubectl get pods
    ```

    Detta kommer utdata för PODEN:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Kontrollera att tjänsten är tillgänglig och hämta IP-adressen:

    ```console
    kubectl get services
    ```

    Detta kommer utdata för den _sentiment_ tjänsten i en POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Nästa steg

* Använder mer [Cognitive Services-behållare](../../cognitive-services-container-support.md)
* Använd den [textanalys ansluten tjänst](../vs-text-connected-service.md)
