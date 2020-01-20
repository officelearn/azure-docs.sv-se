---
title: FÖRÅLDRAD Distribuera behållare med Helm i Azure Kubernetes
description: Använd Helm packnings verktyg för att distribuera behållare i ett Kubernetes-kluster i Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271089"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>FÖRÅLDRAD Använda Helm för att distribuera behållare i ett Kubernetes-kluster

> [!TIP]
> Den uppdaterade versionen av den här artikeln som använder Azure Kubernetes-tjänsten finns i [installera program med Helm i Azure Kubernetes service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. På samma sätt som Linux-paket hanterare som apt-get och yum, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser. Den här artikeln visar hur du arbetar med Helm i ett Kubernetes-kluster som distribueras i Azure Container Service.

Helm har två komponenter: 
* **Helm CLI** är en klient som körs på datorn lokalt eller i molnet  

* **Till** en-server som körs på Kubernetes-klustret och hanterar livs cykeln för dina Kubernetes-program 
 
## <a name="prerequisites"></a>Krav

* [Skapa ett Kubernetes-kluster](container-service-kubernetes-walkthrough.md) i Azure Container Service

* [Installera och konfigurera `kubectl`](../container-service-connect.md) på en lokal dator

* [Installera Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) på en lokal dator

## <a name="helm-basics"></a>Grundläggande om Helm 

Om du vill visa information om det Kubernetes-kluster som du installerar till och distribuerar dina program till, skriver du följande kommando:

```bash
kubectl cluster-info 
```
![kubectl-kluster – info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
När du har installerat Helm installerar du till på ditt Kubernetes-kluster genom att skriva följande kommando:

```bash
helm init --upgrade
```
När den har slutförts visas utdata som liknar följande:

![Till-installation](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Om du vill visa alla Helm-diagram som är tillgängliga i databasen skriver du följande kommando:

```bash 
helm search 
```

Du ser utdata som liknar följande:

![Helm-sökning](./media/container-service-kubernetes-helm/helm-search.png)
 
Om du vill uppdatera diagrammen för att hämta de senaste versionerna skriver du:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Distribuera ett nginx ingress-styrkort 
 
Om du vill distribuera ett nginx-styrkort, anger du ett enda kommando:

```bash
helm install stable/nginx-ingress 
```
![Distribuera ingress-kontrollant](./media/container-service-kubernetes-helm/nginx-ingress.png)

Om du skriver `kubectl get svc` för att visa alla tjänster som körs i klustret, ser du att en IP-adress har tilldelats till ingångs styrenheten. (Medan tilldelningen pågår visas `<pending>`. Det tar några minuter att slutföra.) 

När IP-adressen har tilldelats navigerar du till värdet för den externa IP-adressen för att se nginx-Server delen som körs. 
 
![Ingress IP-adress](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Om du vill se en lista över diagram som är installerade i klustret skriver du:

```bash
helm list 
```

Du kan förkorta kommandot till `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Distribuera ett MariaDB-diagram och-klient

Distribuera nu ett MariaDB-diagram och en MariaDB-klient för att ansluta till databasen.

Om du vill distribuera MariaDB-diagrammet skriver du följande kommando:

```bash
helm install --name v1 stable/mariadb
```

där `--name` är en tagg som används för versioner.

> [!TIP]
> Om distributionen Miss lyckas kör du `helm repo update` och försöker igen.
>
 
 
Om du vill visa alla diagram som har distribuerats i klustret skriver du:

```bash 
helm list
```
 
Om du vill visa alla distributioner som körs i klustret skriver du:

```bash
kubectl get deployments 
``` 
 
 
Slutligen, för att köra en POD för att få åtkomst till klienten, skriver du:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Om du vill ansluta till klienten skriver du följande kommando och ersätter `v1-mariadb` med namnet på distributionen:

```bash
sudo mysql –h v1-mariadb
```
 
 
Nu kan du använda standard-SQL-kommandon för att skapa databaser, tabeller osv. `Create DATABASE testdb1;` skapar till exempel en tom databas. 
 
 
 
## <a name="next-steps"></a>Nästa steg

* Mer information om hur du hanterar Kubernetes-diagram finns i [Helm-dokumentationen](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

