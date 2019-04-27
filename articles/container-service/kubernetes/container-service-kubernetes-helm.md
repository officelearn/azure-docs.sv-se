---
title: (INAKTUELL) Distribuera behållare med Helm i Azure Kubernetes
description: Använda Helm paketering för att distribuera behållare i ett Kubernetes-kluster i Azure Container Service
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 05edbf40e8cd5f8edbdc8b74b540962b1a25c8de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712330"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(INAKTUELL) Använd Helm för att distribuera behållare i ett Kubernetes-kluster

> [!TIP]
> Den uppdaterade versionen som den här artikeln som använder Azure Kubernetes Service, finns i [installera program med Helm i Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) är ett verktyg med öppen källkod paketering som hjälper dig att installera och hantera livscykeln för Kubernetes-program. Liknar Linux pakethanterare, till exempel Apt-get- och Yum, Helm används för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser. Den här artikeln visar hur du arbetar med Helm på ett Kubernetes-kluster som distribueras i Azure Container Service.

Helm består av två komponenter: 
* Den **Helm CLI** är en klient som körs på datorn lokalt eller i molnet  

* **Tiller** är en server som körs på Kubernetes-kluster och hanterar livscykeln för Kubernetes-program 
 
## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa ett Kubernetes-kluster](container-service-kubernetes-walkthrough.md) i Azure Container Service

* [Installera och konfigurera `kubectl` ](../container-service-connect.md) på en lokal dator

* [Installera Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) på en lokal dator

## <a name="helm-basics"></a>Helm-grunderna 

Om du vill visa information om Kubernetes-klustret att du installerar Tiller och distribuera ditt program till, skriver du följande kommando:

```bash
kubectl cluster-info 
```
![kubectl-klusterinformation](./media/container-service-kubernetes-helm/clusterinfo.png)
 
När du har installerat Helm, installera Tiller i Kubernetes-klustret genom att skriva följande kommando:

```bash
helm init --upgrade
```
När processen är klar, kan du se utdata som liknar följande:

![Tiller-installation](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Om du vill visa alla Helm-diagram som finns tillgängliga i databasen, skriver du följande kommando:

```bash 
helm search 
```

Du ser utdata som liknar följande:

![Helm search](./media/container-service-kubernetes-helm/helm-search.png)
 
Om du vill uppdatera diagrammen för att få de senaste versionerna, skriver du:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Distribuera en Nginx ingående controller diagram 
 
Om du vill distribuera en Nginx ingående controller diagram, skriver du ett enda kommando:

```bash
helm install stable/nginx-ingress 
```
![Distribuera ingress-kontrollant](./media/container-service-kubernetes-helm/nginx-ingress.png)

Om du skriver `kubectl get svc` om du vill visa alla tjänster som körs på klustret som du ser att en IP-adress har tilldelats ingress-kontrollant. (Medan tilldelningen pågår kan du se `<pending>`. Det tar några minuter att slutföra.) 

Efter IP-adress tilldelas, navigerar till värdet för den externa IP-adressen för att se Nginx-serverdel som körs. 
 
![Inkommande IP-adress](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Om du vill se en lista över diagrammen som installerats på ditt kluster, skriver du:

```bash
helm list 
```

Du kan förkorta kommandot till `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Distribuera en MariaDB diagram och klient

Nu distribuera ett MariaDB-diagram och en MariaDB-klient för att ansluta till databasen.

Om du vill distribuera MariaDB-diagram, skriver du följande kommando:

```bash
helm install --name v1 stable/mariadb
```

där `--name` är en tagg som används för versioner.

> [!TIP]
> Om distributionen inte köra `helm repo update` och försök igen.
>
 
 
Om du vill visa alla diagram som har distribuerat i klustret, skriver du:

```bash 
helm list
```
 
Om du vill visa alla distributioner som körs på klustret, skriver du:

```bash
kubectl get deployments 
``` 
 
 
Slutligen, om du vill köra en pod för att komma åt klienten, skriver du:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
För att ansluta till klienten, skriver du följande kommando, ersätta `v1-mariadb` med namnet på din distribution:

```bash
sudo mysql –h v1-mariadb
```
 
 
Du kan nu använda standard SQL-kommandon för att skapa databaser, tabeller osv. Till exempel `Create DATABASE testdb1;` skapar en tom databas. 
 
 
 
## <a name="next-steps"></a>Nästa steg

* Mer information om hur du hanterar Kubernetes diagram finns i den [Helm dokumentation](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

