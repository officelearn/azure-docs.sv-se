---
title: "Snabbstart för Kubernetes-kluster i Azure | Microsoft Docs"
description: "Distribuera och komma igång med ett Kubernetes-kluster i Azure Container Service"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e4f47341554e2de514c8be2f5c85983d09bbb760
ms.lasthandoff: 04/03/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Kom igång med ett Kubernetes-kluster i Container Service


Anvisningarna i den här artikeln beskriver hur du använder Azure CLI 2.0-kommandon för att skapa ett Kubernetes-kluster. Därefter kan du använda kommandoradsverktyget `kubectl` för att börja arbeta med behållare i klustret.

Bilden nedan visar arkitekturen för ett Container Service-kluster med en huvudnod och två agenter. Kubernetes REST-API:et använder huvudnoden. Agentnoderna är grupperade i en Azure-tillgänglighetsuppsättning och kör dina behållare. Alla virtuella datorer finns i samma privata virtuella nätverk och kan nås av varandra.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Krav
I den här genomgången förutsätter vi att du har installerat och konfigurerat [Azure CLI v. 2.0](/cli/azure/install-az-cli2). Du måste också ha en offentlig SSH RSA-nyckel i `~/.ssh/id_rsa.pub`. Om du inte har någon nyckel läser du anvisningarna för [OS X och Linux](../virtual-machines/linux/mac-create-ssh-keys.md) eller [Windows](../virtual-machines/linux/ssh-from-windows.md).






## <a name="create-your-kubernetes-cluster"></a>Skapa Kubernetes-klustret

Här är några exempel på shell-kommandon som använder Azure CLI 2.0 för att skapa klustret. Mer information finns i [Skapa ett Azure Container Service-kluster med hjälp av Azure CLI 2.0](container-service-create-acs-cluster-cli.md).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
För att kunna skapa klustret måste du först skapa en resursgrupp på en specifik plats. Kör kommandon som följande:

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Skapa ett kluster
När du har skapat en resursgrupp kan du skapa ett kluster i gruppen:

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Under distributionen överför CLI `~/.ssh/id_rsa.pub` till de virtuella Linux-datorerna.
>

När kommandot har slutförts bör du ha ett fungerande Kubernetes-kluster.

### <a name="connect-to-the-cluster"></a>Anslut till klustret

Följande är exempel på Azure CLI-kommandon som du använder för att ansluta till Kubernetes-klustret från klientdatorn med hjälp av `kubectl`, Kubernetes kommandoradsklient. Mer information finns i [Ansluta till ett Azure Container Service-kluster](container-service-connect.md).

Om du inte redan har installerat `kubectl` kan du göra det med:

```console
az acs kubernetes install-cli
```

När `kubectl` har installerats kör du följande kommando för att hämta huvudkonfigurationen för Kubernetes till ~/.kube/config-filen:

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Nu bör du kunna komma åt ditt kluster från datorn. Prova att köra:
```console
kubectl get nodes
```

Kontrollera att du kan se en lista över datorerna i klustret.

## <a name="create-your-first-kubernetes-service"></a>Skapa din första Kubernetes-tjänst

I den här genomgången lär du dig att:
 * distribuera och exponera ett Docker-program
 * använda `kubectl exec` för att köra kommandon i en behållare 
 * komma åt Kubernetes-instrumentpanelen.

### <a name="start-a-simple-container"></a>Starta en enkel behållare
Du kan köra en enkel behållare (i det här fallet Nginx-webbservern) genom att köra:

```console
kubectl run nginx --image nginx
```

Det här kommandot startar Docker-behållaren Nginx i en pod på en av noderna.

Visa behållaren som körs genom att köra:

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Exponera tjänsten för världen
Om du vill exponera tjänsten på Internet skapar du en Kubernetes-`Service` av typen `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

När du gör det skapar Kubernetes en Azure-belastningsutjämningsregel med en offentlig IP-adress. Det tar några minuter för ändringen att distribueras till belastningsutjämnaren. Mer information finns i [Load balance containers in a Kubernetes cluster in Azure Container Service](container-service-kubernetes-load-balancing.md) (Belastningsutjämna behållare i ett Kubernetes-kluster i Azure Container Service).

Kör följande kommando för att se hur tjänsten ändras från `pending` till att visa en extern IP-adress:

```console
watch 'kubectl get svc'
```

  ![Bild av övergången från väntande till extern IP-adress](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

När du ser den externa IP-adressen kan du gå till den i webbläsaren:

  ![Bild som visar hur du går till Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navigera i Kubernetes-användargränssnittet
Du kan visa Kubernetes-webbgränssnittet genom att använda:

```console
kubectl proxy
```
Den här raden kör en enkel autentiserad proxy på localhost som du kan använda för att visa Kubernetes-webbanvändargränssnittet som körs på [http://localhost:8001/ui](http://localhost:8001/ui). Mer information finns i [Using the Kubernetes web UI with Azure Container Service](container-service-kubernetes-ui.md) (Använda Kubernetes-webbanvändargränssnittet med Azure Container Service).

![Bild av Kubernetes-instrumentpanelen](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Fjärrsessioner i dina behållare
Med Kubernetes kan du köra kommandon i en fjärransluten Docker-behållare som körs i klustret.

```console
# Get the name of your nginx pods
kubectl get pods
```

Genom att använda ditt podnamn kan du köra ett fjärrkommando på din pod.  Exempel:

```console
kubectl exec <pod name> date
```

Du kan också upprätta en helt interaktiv session med `-it`-flaggorna:

```console
kubectl exec <pod name> -it bash
```

![Fjärrsession i en behållare](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Nästa steg

Information om fler åtgärder som du kan utföra med ett Kubernetes-kluster finns i följande resurser:

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – visar hur du distribuerar, skalar, uppdaterar och felsöker program som använder behållare.
* [Användarhandboken för Kubernetes](http://kubernetes.io/docs/user-guide/) – innehåller information om hur du kör program i ett befintligt Kubernetes-kluster.
* [Kubernetes-exempel](https://github.com/kubernetes/kubernetes/tree/master/examples) – innehåller exempel på hur du kör verkliga program med Kubernetes.

