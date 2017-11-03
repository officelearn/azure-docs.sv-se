---
title: "Kubernetes på Azure tutorial – distribuera programmet | Microsoft Docs"
description: "AKS tutorial – distribuera program"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 73b943709fb3846058d4b1c09bc76b09460855ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Köra program i Azure Container Service (AKS)

I den här självstudiekursen del fyra åtta, ett exempelprogram har distribuerats i ett Kubernetes kluster. Slutfört stegen innefattar:

> [!div class="checklist"]
> * Uppdatera Kubernetes manifestfiler
> * Kör program i Kubernetes
> * Testa programmet

Det här programmet skalas ut, uppdateras i efterföljande självstudiekurser och Operations Management Suite som konfigurerats för att övervaka Kubernetes klustret.

Den här kursen förutsätter en grundläggande förståelse för begrepp Kubernetes detaljerad information om Kubernetes finns i [Kubernetes dokumentationen](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudier, ett program som har paketerats till en behållare bild, bilden överfördes till registret för Azure-behållare och en Kubernetes klustret har skapats. 

Den här kursen behöver du i förväg skapade `azure-vote-all-in-one-redis.yml` Kubernetes manifestfil. Den här filen hämtades i föregående självstudiekursen med programmets källkod. Kontrollera att du har klona lagringsplatsen och att du har ändrat kataloger till klonade lagringsplatsen.

Om du inte har gjort dessa steg och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Uppdatera manifestfilen

Azure Container registret (ACR) har använts att lagra en avbildning av behållare i den här självstudiekursen. ACR server inloggningsnamnet måste uppdateras i manifestfilen Kubernetes innan du kör programmet.

Hämta ACR server inloggningsnamnet med den [az acr lista](/cli/azure/acr#list) kommando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Manifestfilen har skapats i förväg med inloggningen servernamnet `microsoft`. Öppna filen med en textredigerare. I det här exemplet filen har öppnats med `vi`.

```console
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

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yml
```

Resultat:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testa program

En [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) skapas som visar programmet till internet. Den här processen kan ta några minuter. 

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.7/#get) med argumentet `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Till en början visas *EXTERNAL-IP* för *azure-vote-front*-tjänsten som *pending* (väntande).
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

En gång i *externa IP-* adress har ändrats från *väntande* till en *IP-adress*, använda `CTRL-C` att stoppa kubectl titta på processen. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Bläddra till den externa IP-adressen om du vill se programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har programmet Azure rösten distribuerats till ett kluster med Kubernetes på AKS. Aktiviteter har slutförts är:  

> [!div class="checklist"]
> * Hämta Kubernetes manifestfiler
> * Kör programmet i Kubernetes
> * Testa programmet

Gå vidare till nästa kurs att lära dig om att skala både ett Kubernetes program och den underliggande Kubernetes-infrastrukturen. 

> [!div class="nextstepaction"]
> [Skala Kubernetes program och infrastruktur](./tutorial-kubernetes-scale.md)
