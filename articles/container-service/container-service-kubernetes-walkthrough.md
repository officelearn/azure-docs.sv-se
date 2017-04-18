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
ms.date: 04/05/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5c529ae41b42d276d37e6103305e33ed04694e18
ms.lasthandoff: 04/07/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Kom igång med ett Kubernetes-kluster i Container Service


Den här genomgången visar hur du kan använda Azure CLI 2.0-kommandon för att skapa ett Kubernetes-kluster i Azure Container Service. Därefter kan du använda kommandoradsverktyget `kubectl` för att börja arbeta med behållare i klustret.

Bilden nedan visar arkitekturen för ett Container Service-kluster med en huvudnod och två agenter. Kubernetes REST-API:et använder huvudnoden. Agentnoderna är grupperade i en Azure-tillgänglighetsuppsättning och kör dina behållare. Alla virtuella datorer finns i samma privata virtuella nätverk och kan nås av varandra.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Krav
I den här genomgången förutsätter vi att du har installerat och konfigurerat [Azure CLI 2.0](/cli/azure/install-az-cli2). 

Dessa exempelkommandon förutsätter att du kör Azure CLI i bash-gränssnittet, vilket är vanligt på Linux och macOS. Om du kör Azure CLI på en Windows-klient kan vissa skript och filsyntax avvika beroende på ditt kommandogränssnitt. 

## <a name="create-your-kubernetes-cluster"></a>Skapa Kubernetes-klustret

Här är några exempel på shell-kommandon som använder Azure CLI 2.0 för att skapa klustret. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
För att kunna skapa klustret måste du först skapa en resursgrupp på en specifik plats. Kör kommandon som följande:

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Skapa ett kluster
När du har skapat en resursgrupp kan du skapa ett kluster i gruppen. I följande exempel används alternativet `--generate-ssh-keys` som genererar offentliga och privata SSH-nyckelfiler som behövs för att distribuera om de inte finns redan i standardkatalogen `~/.ssh/`. 

Det här kommandot genererar automatiskt huvudnamnet för [Azure Active Directory-tjänsten ](container-service-kubernetes-service-principal.md) som används av ett Kubernetes-kluster i Azure.

```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```


Kommandot har slutförts och du bör ha ett fungerande Kubernetes-kluster efter ett par minuter.

### <a name="connect-to-the-cluster"></a>Anslut till klustret

För att ansluta till Kubernetes-klustret från klientdatorn använder du [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes kommandoradsklient. 

Om du inte redan har installerat `kubectl` kan du göra det med:

```azurecli
sudo az acs kubernetes install-cli
```
> [!TIP]
> Som standard installerar det här kommandot den här `kubectl` binära filen `/usr/local/bin/kubectl` på ett Linux- eller macOS-system, eller på `C:\Program Files (x86)\kubectl.exe` i Windows. Om du vill ange en annan installationssökväg använder du parametern `--install-location`.
>

När `kubectl` är installerat, se till att dess katalog finns i systemsökvägen eller lägg till det i sökvägen. 


Sedan kör du följande kommando för att hämta huvudkonfigurationen för Kubernetes till `~/.kube/config`-filen:

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Anvisningar för hur du installerar och konfigurerar `kubectl` finns i [Ansluta till ett Azure Container Service-kluster](container-service-connect.md).

Nu bör du kunna komma åt ditt kluster från datorn. Prova att köra:

```bash
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

```bash
kubectl run nginx --image nginx
```

Det här kommandot startar Docker-behållaren Nginx i en pod på en av noderna.

Visa behållaren som körs genom att köra:

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Exponera tjänsten för världen
Om du vill exponera tjänsten på Internet skapar du en Kubernetes-`Service` av typen `LoadBalancer`:

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Genom detta kommando skapar Kubernetes en Azure Load Balancer-regel med en offentlig IP-adress. Det tar några minuter för ändringen att distribueras till belastningsutjämnaren. Mer information finns i [Load balance containers in a Kubernetes cluster in Azure Container Service](container-service-kubernetes-load-balancing.md) (Belastningsutjämna behållare i ett Kubernetes-kluster i Azure Container Service).

Kör följande kommando för att se hur tjänsten ändras från `pending` till att visa en extern IP-adress:

```bash
watch 'kubectl get svc'
```

  ![Bild av övergången från väntande till extern IP-adress](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

När du ser den externa IP-adressen kan du gå till den i webbläsaren:

  ![Bild som visar hur du går till Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navigera i Kubernetes-användargränssnittet
Du kan visa Kubernetes-webbgränssnittet genom att använda:

```bash
kubectl proxy
```
Det här kommandot kör en enkel autentiserad proxy på localhost som du kan använda för att visa Kubernetes-webbanvändargränssnittet som körs på [http://localhost:8001/ui](http://localhost:8001/ui). Mer information finns i [Using the Kubernetes web UI with Azure Container Service](container-service-kubernetes-ui.md) (Använda Kubernetes-webbanvändargränssnittet med Azure Container Service).

![Bild av Kubernetes-instrumentpanelen](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Fjärrsessioner i dina behållare
Med Kubernetes kan du köra kommandon i en fjärransluten Docker-behållare som körs i klustret.

```bash
# Get the name of your nginx pods
kubectl get pods
```

Genom att använda ditt podnamn kan du köra ett fjärrkommando på din pod.  Exempel:

```bash
kubectl exec <pod name> date
```

Du kan också upprätta en helt interaktiv session med `-it`-flaggorna:

```bash
kubectl exec <pod name> -it bash
```

![Fjärrsession i en behållare](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Nästa steg

Information om fler åtgärder som du kan utföra med ett Kubernetes-kluster finns i följande resurser:

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – visar hur du distribuerar, skalar, uppdaterar och felsöker program som använder behållare.
* [Användarhandboken för Kubernetes](http://kubernetes.io/docs/user-guide/) – innehåller information om hur du kör program i ett befintligt Kubernetes-kluster.
* [Kubernetes-exempel](https://github.com/kubernetes/kubernetes/tree/master/examples) – innehåller exempel på hur du kör verkliga program med Kubernetes.

