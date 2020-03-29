---
title: Nyckelfrasextrahering Kubernetes config och distribuera steg
titleSuffix: Azure Cognitive Services
description: Nyckelfrasextrahering Kubernetes config och distribuera steg
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1a96b5e4d03ce72bac29126028ca61e11e8c7324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262102"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Distribuera behållaren för extrahering av nyckelfraser till ett AKS-kluster

1. Öppna Azure CLI och logga in på Azure.

    ```azurecli
    az login
    ```

1. Logga in i AKS-klustret. Ersätt `your-cluster-name` `your-resource-group` och med lämpliga värden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    När det här kommandot har körts rapporterar det ett meddelande som liknar följande:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Om du har flera prenumerationer tillgängliga för `az aks get-credentials` dig på ditt Azure-konto och kommandot returnerar med ett fel, är ett vanligt problem att du använder fel prenumeration. Ange kontexten för din Azure CLI-session för att använda samma prenumeration som du skapade resurserna med och försök igen.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öppna valfri textredigerare. I det här exemplet används Visual Studio-kod.

    ```console
    code .
    ```

1. Skapa en ny fil med namnet *keyphrase.yaml*i textredigeraren och klistra in följande YAML i den. Var noga `billing/value` med `apikey/value` att ersätta och med din egen information.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Spara filen och stäng textredigeraren.
1. Kör kommandot Kubernetes `apply` med filen *keyphrase.yaml* som mål:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    När kommandot har installerats har distributionskonfigurationen visas ett meddelande som liknar följande utdata:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Kontrollera att podden har distribuerats:

    ```console
    kubectl get pods
    ```

    Utdata för poddens körstatus:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Kontrollera att tjänsten är tillgänglig och hämta IP-adressen.

    ```console
    kubectl get services
    ```

    Utdata för *nyckelfrastjänstens* körstatus i podden:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
