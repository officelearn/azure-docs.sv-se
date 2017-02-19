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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service Engine – Genomgång av Kubernetes

## <a name="prerequisites"></a>Krav
I den här genomgången förutsätter vi att du har installerat [”azure-cli”-kommandoradsverktyget](https://github.com/azure/azure-cli#installation) och att du har skapat en [offentlig SSH-nyckel](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) i `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Översikt

Anvisningarna nedan skapar ett Kubernetes-kluster med en huvudnod och två arbetarnoder.
Kubernetes REST-API:et använder huvudnoden.  Arbetarnoderna är grupperade i en Azure-tillgänglighetsuppsättning och kör dina behållare. Alla virtuella datorer finns i samma privata virtuella nätverk och är helt tillgängliga för varandra.

> [!NOTE]
> Stöd för Kubernetes i Azure Container Service förhandsvisas just nu.
>

Följande bild visar arkitekturen för ett Container Service-kluster med en huvudnod och två agenter:

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Skapa ditt Kubernetes-kluster

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
För att skapa ditt kluster måste du först skapa en resursgrupp på en specifik plats. Du kan göra det med:
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Skapa ett kluster
När du har en resursgrupp kan du skapa ett kluster i den gruppen med:
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Azure-cli överför `~/.ssh/id_rsa.pub` till de virtuella Linux-datorerna.
>

När kommandot har slutförts bör du ha ett fungerande Kubernetes-kluster.

### <a name="configure-kubectl"></a>Konfigurera kubectl
`kubectl` är Kubernetes-kommandoradsklienten.  Om du inte redan har installerat det kan du installera det med:

```console
az acs kubernetes install-cli
```

När `kubectl` har installerats kan du köra kommandot nedan för att hämta kubernetes-huvudklusterkonfigurationen till filen ~/.kube/config
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

Nu bör du kunna komma åt ditt kluster från datorn. Prova att köra:
```console
kubectl get nodes
```

Och kontrollera att du kan se datorerna i klustret.

## <a name="create-your-first-kubernetes-service"></a>Skapa din första Kubernetes-tjänst

I den här genomgången lär du dig att:
 * distribuera och exponera ett Docker-program
 * använda `kubectl exec` för att köra kommandon i en behållare 
 * komma åt Kubernetes-instrumentpanelen.

### <a name="start-a-simple-container"></a>Starta en enkel behållare
Du kan köra en enkel behållare (i det här fallet `nginx`-webbservern) genom att köra:

```console
kubectl run nginx --image nginx
```

Det här kommandot startar Docker-behållaren nginx i en pod på en av noderna.

Du kan köra
```console
kubectl get pods
```

för att se behållaren som körs.

### <a name="expose-the-service-to-the-world"></a>Exponera tjänsten för världen
Du kan exponera tjänsten för världen.  Du skapar en Kubernetes-`Service` av typen `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Detta gör nu att Kubernetes skapar en Azure-belastningsutjämnare med en offentlig IP-adress. Det tar runt 2–3 minuter för ändringen att spridas till belastningsutjämnaren.

Så här ser du hur tjänsten ändras från ”väntande” till en extern IP-adress:
```console
watch 'kubectl get svc'
```

  ![Bild av övergången från väntande till extern IP-adress](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

När du ser den externa IP-adressen kan du bläddra till den i webbläsaren:

  ![Bild som visar hur du går till nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navigera i Kubernetes-användargränssnittet
Du kan visa Kubernetes-webbgränssnittet genom att använda:

```console
kubectl proxy
```
Den här raden kör en enkel autentiserad proxy på localhost som du kan använda för att visa [Kubernetes-användargränssnittet](http://localhost:8001/ui)

![Bild av Kubernetes-instrumentpanelen](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Fjärrsessioner i dina behållare
Med Kubernetes kan du köra kommandon i en fjärransluten Docker-behållare som körs i klustret.

```console
# Get the name of your nginx pod
kubectl get pods
```

Genom att använda ditt podnamn kan du köra ett fjärrkommando på din pod.  Exempel:
```console
kubectl exec nginx-701339712-retbj date
```

Du kan också upprätta en helt interaktiv session med `-it`-flaggorna:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Bild av curl till podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Detaljer
### <a name="installing-the-kubectl-configuration-file"></a>Installera kubectl-konfigurationsfilen
När du körde `az acs kubernetes get-credentials` lagrades kube-konfigurationsfilen för fjärråtkomst under arbetskatalogen ~/.kube/config.

Om du någon gång behöver hämta den direkt kan du använda `ssh` i Linux- eller OS X, eller `Putty` i Windows:

#### <a name="windows"></a>Windows
Du kan använda pscp från [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Kontrollera att ditt certifikat exponeras via [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X eller Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>Mer information

### <a name="azure-container-service"></a>Azure Container Service

1. [Dokumentation om Azure Container Service](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure Container Service Open Source Engine](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Kubernetes-community – dokumentation

1. [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – visar hur du distribuerar, skalar, uppdaterar och felsöker program som använder behållare.
2. [Användarhandboken för Kubernetes](http://kubernetes.io/docs/user-guide/) – innehåller information om hur du kör program i ett befintligt Kubernetes-kluster.
3. [Kubernetes-exempel](https://github.com/kubernetes/kubernetes/tree/master/examples) – innehåller ett antal exempel på hur du kör verkliga program med Kubernetes.



<!--HONumber=Jan17_HO4-->


