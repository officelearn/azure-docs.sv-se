---
title: (FÖRÅLDRAD) Distribuera behållare med Helm i Azure Kubernetes
description: Använda Helm-förpackningsverktyget för att distribuera behållare på ett Kubernetes-kluster i Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271089"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(FÖRÅLDRAD) Använda Helm för att distribuera behållare i ett Kubernetes-kluster

> [!TIP]
> Den uppdaterade versionen som används i den här artikeln som använder Azure Kubernetes-tjänsten finns [i Installera program med Helm i Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) är ett förpackningsverktyg med öppen källkod som hjälper dig att installera och hantera livscykeln för Kubernetes-program. I likhet med Linux-pakethanterare som Apt-get och Yum används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser. Den här artikeln visar hur du arbetar med Helm på ett Kubernetes-kluster som distribueras i Azure Container Service.

Helm har två komponenter: 
* **Helm CLI** är en klient som körs på din dator lokalt eller i molnet  

* **Tiller** är en server som körs i Kubernetes-klustret och hanterar livscykeln för dina Kubernetes-program 
 
## <a name="prerequisites"></a>Krav

* [Skapa ett Kubernetes-kluster](container-service-kubernetes-walkthrough.md) i Azure Container Service

* [Installera och `kubectl` konfigurera](../container-service-connect.md) på en lokal dator

* [Installera Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) på en lokal dator

## <a name="helm-basics"></a>Helm grunderna 

Om du vill visa information om Kubernetes-klustret som du installerar Tiller och distribuerar dina program till skriver du följande kommando:

```bash
kubectl cluster-info 
```
![kubectl kluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
När du har installerat Helm installerar du Tiller i Kubernetes-klustret genom att skriva följande kommando:

```bash
helm init --upgrade
```
När den är klar visas utdata som följande:

![Tillrkare installation](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Om du vill visa alla Helm-diagram som är tillgängliga i databasen skriver du följande kommando:

```bash 
helm search 
```

Du ser utdata som följande:

![Helm sökning](./media/container-service-kubernetes-helm/helm-search.png)
 
Om du vill uppdatera diagrammen för att hämta de senaste versionerna skriver du:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Distribuera ett Nginx-kontrollantdiagram 
 
Om du vill distribuera ett Nginx-ingresskontrolldiagram skriver du ett enda kommando:

```bash
helm install stable/nginx-ingress 
```
![Distribuera ingående styrenhet](./media/container-service-kubernetes-helm/nginx-ingress.png)

Om du `kubectl get svc` skriver för att visa alla tjänster som körs i klustret ser du att en IP-adress har tilldelats den ingående styrenheten. (Medan tilldelningen pågår visas `<pending>`. Det tar ett par minuter att slutföra.) 

När IP-adressen har tilldelats navigerar du till värdet för den externa IP-adressen för att se Nginx-backend som körs. 
 
![Inkommande IP-adress](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Om du vill visa en lista över diagram som är installerade i klustret skriver du:

```bash
helm list 
```

Du kan förkorta kommandot `helm ls`till .
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Distribuera ett MariaDB-diagram och en klient

Distribuera nu ett MariaDB-diagram och en MariaDB-klient för att ansluta till databasen.

Om du vill distribuera MariaDB-diagrammet skriver du följande kommando:

```bash
helm install --name v1 stable/mariadb
```

var `--name` finns en tagg som används för versioner.

> [!TIP]
> Om distributionen misslyckas kör du `helm repo update` och försöker igen.
>
 
 
Om du vill visa alla diagram som distribueras i klustret skriver du:

```bash 
helm list
```
 
Om du vill visa alla distributioner som körs i klustret skriver du:

```bash
kubectl get deployments 
``` 
 
 
Slutligen, för att köra en pod för att komma åt klienten, skriver du:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Om du vill ansluta till klienten `v1-mariadb` skriver du följande kommando och ersätter med namnet på distributionen:

```bash
sudo mysql –h v1-mariadb
```
 
 
Du kan nu använda vanliga SQL-kommandon för att skapa databaser, tabeller, etc. Skapar till `Create DATABASE testdb1;` exempel en tom databas. 
 
 
 
## <a name="next-steps"></a>Nästa steg

* Mer information om hur du hanterar Kubernetes-diagram finns i [Helm-dokumentationen](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

