---
title: "Självstudiekurs för Azure Container Service - distribuera programmet | Microsoft Docs"
description: "Självstudiekurs för Azure Container Service - distribuera program"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2c9563a38f0aedcb6f67fd44031a47a712088ddc
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="run-applications-in-kubernetes"></a>Köra program i Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

I den här självstudiekursen del fyra av sju, ett exempelprogram har distribuerats i ett Kubernetes kluster. Slutfört stegen innefattar:

> [!div class="checklist"]
> * Uppdatera Kubernetes manifestfiler
> * Kör program i Kubernetes
> * Testa programmet

Det här programmet skalas ut, uppdateras i efterföljande självstudiekurser och Operations Management Suite som konfigurerats för att övervaka Kubernetes klustret.

Den här kursen förutsätter en grundläggande förståelse för begrepp Kubernetes detaljerad information om Kubernetes finns i [Kubernetes dokumentationen](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudier, ett program som har paketerats till en behållare bild, bilden överfördes till registret för Azure-behållare och en Kubernetes klustret har skapats. 

Den här kursen behöver du i förväg skapade `azure-vote-all-in-one-redis.yml` Kubernetes manifestfil. Den här filen hämtades i föregående självstudiekursen med programmets källkod. Kontrollera att du har klona lagringsplatsen och att du har ändrat kataloger till klonade lagringsplatsen.

Om du inte har gjort dessa steg och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Uppdatera manifestfilen

Azure Container registret (ACR) har använts att lagra en avbildning av behållare i den här självstudiekursen. ACR server inloggningsnamnet måste uppdateras i manifestfilen Kubernetes innan du kör programmet.

Hämta ACR server inloggningsnamnet med den [az acr lista](/cli/azure/acr#list) kommando.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Manifestfilen har skapats i förväg med inloggningen servernamnet `microsoft`. Öppna filen med en textredigerare. I det här exemplet filen har öppnats med `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Ersätt `microsoft` med ACR server inloggningsnamnet. Det här värdet finns på rad **47** för manifestfilen.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Spara och stäng filen.

## <a name="deploy-application"></a>Distribuera program

Använd kommandot [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) för att köra programmet. Det här kommandot Parsar manifestfilen och skapa de definierade Kubernetes-objekt.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Resultat:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testa program

En [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) skapas som visar programmet till internet. Den här processen kan ta några minuter. 

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) med argumentet `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Först den **externa IP-** för den `azure-vote-front` tjänsten visas som `pending`. När den extern IP-adressen har ändrats från `pending` till en `IP address`, Använd `CTRL-C` att stoppa kubectl titta på processen.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Bläddra till den externa IP-adressen om du vill se programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen distribuerades Azure rösten programmet till ett Azure Container Service Kubernetes-kluster. Aktiviteter har slutförts är:  

> [!div class="checklist"]
> * Hämta Kubernetes manifestfiler
> * Kör programmet i Kubernetes
> * Testa programmet

Gå vidare till nästa kurs att lära dig om att skala både ett Kubernetes program och den underliggande Kubernetes-infrastrukturen. 

> [!div class="nextstepaction"]
> [Skala Kubernetes program och infrastruktur](./container-service-tutorial-kubernetes-scale.md)