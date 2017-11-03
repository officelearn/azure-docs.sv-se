---
title: "Distribuera behållare med Helm i Azure Kubernetes | Microsoft Docs"
description: "Verktyget Helm paketering ska distribuera behållare på ett Kubernetes kluster i Azure Container Service"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 9dd137de99864fbac2c73400a88956f6afa324db
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Använda Helm för att distribuera behållare på ett Kubernetes kluster

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Helm](https://github.com/kubernetes/helm/) är en öppen källkod paketering verktyg som hjälper dig att installera och hantera livscykeln för Kubernetes program. Liknar Linux paketet chefer som lgh get och Yum, Helm används för att hantera Kubernetes diagram som är paket med förkonfigurerade Kubernetes resurser. Den här artikeln visar hur du arbetar med Helm på ett Kubernetes kluster som distribueras i Azure Container Service.

Helm består av två komponenter: 
* Den **Helm CLI** är en klient som körs på datorn lokalt eller i molnet  

* **Rorkulten** är en server som körs på klustret Kubernetes och hanterar livscykeln för Kubernetes-program 
 
## <a name="prerequisites"></a>Krav

* [Skapa ett kluster med Kubernetes](container-service-kubernetes-walkthrough.md) i Azure Container Service

* [Installera och konfigurera `kubectl` ](../container-service-connect.md) på en lokal dator

* [Installera Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) på en lokal dator

## <a name="helm-basics"></a>Helm grunderna 

Om du vill visa information om Kubernetes klustret att du installerar rorkulten och distribuera ditt program till, skriver du följande kommando:

```bash
kubectl cluster-info 
```
![kubectl klusterinformation](./media/container-service-kubernetes-helm/clusterinfo.png)
 
När du har installerat Helm installera rorkulten på Kubernetes klustret genom att skriva följande kommando:

```bash
helm init --upgrade
```
När den är klar kan du se utdata som liknar följande:

![Rorkulten installation](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Om du vill visa alla Helm-diagram som finns tillgängliga i databasen, skriver du följande kommando:

```bash 
helm search 
```

Du kan se utdata som liknar följande:

![Helm sökning](./media/container-service-kubernetes-helm/helm-search.png)
 
Om du vill uppdatera diagram om du vill hämta den senaste versionen, skriver du:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Distribuera ett Nginx ingång controller diagram 
 
Om du vill distribuera ett Nginx ingång controller diagram, skriver du ett enda kommando:

```bash
helm install stable/nginx-ingress 
```
![Distribuera ingång-styrenhet](./media/container-service-kubernetes-helm/nginx-ingress.png)

Om du anger `kubectl get svc` om du vill visa alla tjänster som körs på klustret som du ser att en IP-adress har tilldelats ingång-styrenhet. (När tilldelningen pågår kan du se `<pending>`. Det tar några minuter att slutföra.) 

Efter IP-adress tilldelas, navigerar till värdet för externa IP-adressen finns Nginx-backend som körs. 
 
![Ingång IP-adress](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Om du vill se en lista över diagram som installerats på klustret, skriver du:

```bash
helm list 
```

Du kan förkorta kommandot till `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Distribuera ett MariaDB diagram och klient

Nu distribuera MariaDB diagram och en MariaDB klient ansluta till databasen.

Om du vill distribuera MariaDB diagram, skriver du följande kommando:

```bash
helm install --name v1 stable/mariadb
```

där `--name` är en tagg som används för versioner.

> [!TIP]
> Om distributionen av misslyckas kör `helm repo update` och försök igen.
>
 
 
Om du vill visa alla diagram som distribuerats på klustret, skriver du:

```bash 
helm list
```
 
Om du vill visa alla distributioner som körs på klustret, skriver du:

```bash
kubectl get deployments 
``` 
 
 
Slutligen, om du vill köra en baljor för att komma åt klienten, skriver du:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
För att ansluta till klienten, Skriv följande kommando ersätter `v1-mariadb` med namnet på din distribution:

```bash
sudo mysql –h v1-mariadb
```
 
 
Du kan nu använda standard SQL-kommandon för att skapa databaser, tabeller osv. Till exempel `Create DATABASE testdb1;` skapar en tom databas. 
 
 
 
## <a name="next-steps"></a>Nästa steg

* Mer information om hur du hanterar Kubernetes diagram finns det [Helm dokumentationen](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

