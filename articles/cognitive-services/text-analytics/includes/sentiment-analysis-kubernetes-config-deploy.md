---
title: Attitydanalys Kubernetes konfiguration och distribution av steg
titleSuffix: Azure Cognitive Services
description: Attitydanalys Kubernetes konfiguration och distribution av steg
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: bd93773e4d3c5e06bca752612dac6c563a2f5da1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383411"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Distribuera Attitydanalys-behållaren till ett AKS-kluster

1. Öppna Azure CLI och logga in på Azure.

    ```azurecli
    az login
    ```

1. Logga in på AKS-klustret. Ersätt `your-cluster-name` och `your-resource-group` med lämpliga värden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    När det här kommandot körs rapporterar det ett meddelande som liknar följande:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Om du har flera tillgängliga prenumerationer på ditt Azure-konto och `az aks get-credentials` kommandot returnerar med ett fel, är det ett vanligt problem att du använder fel prenumeration. Ställ in kontexten för Azure CLI-sessionen så att den använder samma prenumeration som du skapade resurserna med och försök igen.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öppna valfri text redigerare. I det här exemplet används Visual Studio Code.

    ```azurecli
    code .
    ```

1. I text redigeraren skapar du en ny fil med namnet *sentiment. yaml*och klistrar in följande yaml i den. Se till att ersätta `billing/value` och `apikey/value` med din egen information.

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
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
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
1. Kör kommandot Kubernetes `apply` med filen *sentiment. yaml* som mål:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    När kommandot har tillämpat distributions konfigurationen visas ett meddelande som liknar följande utdata:

    ```console
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Kontrol lera att Pod har distribuerats:

    ```console
    kubectl get pods
    ```

    Utdata för körnings status för pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Kontrol lera att tjänsten är tillgänglig och hämta IP-adressen.

    ```console
    kubectl get services
    ```

    Utdata för körnings statusen för *sentiment* -tjänsten i pod:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
