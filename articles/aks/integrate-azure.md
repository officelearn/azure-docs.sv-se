---
title: Integrera med Azure-hanterade tjänster med Open Service Broker for Azure (OSBA)
description: Integrera med Azure-hanterade tjänster med Open Service Broker for Azure (OSBA)
services: container-service
author: sozercan
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: 74240298b0c8bec46ab2beab6fcdfbb59fd7b12a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579982"
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integrera med Azure-hanterade tjänster med Open Service Broker for Azure (OSBA)

Tillsammans med [Kubernetes-tjänstkatalogen][kubernetes-service-catalog] gör Open Service Broker for Azure (OSBA) att utvecklare kan utnyttja Azure-hanterade tjänster i Kubernetes. Den här guiden fokuserar på att distribuera-tjänstkatalogen, Open Service Broker for Azure (OSBA) och program som använder Azure-hanterade tjänster med Kubernetes.

## <a name="prerequisites"></a>Nödvändiga komponenter
* En Azure-prenumeration

* Azure CLI: [installera det lokalt][azure-cli-install] eller använd det i [Azure Cloud Shell][azure-cloud-shell].

* Helm CLI 2.7+: [installera det lokalt][helm-cli-install] eller använd det i [Azure Cloud Shell][azure-cloud-shell].

* Behörigheter för att skapa tjänstens huvudnamn med deltagarrollen på din Azure-prenumeration

* Ett befintligt AKS-kluster (Azure Kubernetes Service). Om du behöver ett AKS-kluster följer du snabbstarten [Skapa ett AKS-kluster][create-aks-cluster].

## <a name="install-service-catalog"></a>Installera tjänstkatalogen

Det första steget är att installera tjänstkatalogen i ditt Kubernetes-kluster med ett Helm-diagram. Uppgradera din Tiller-installation (Helm-server) i klustret med:

```azurecli-interactive
helm init --upgrade
```

Lägg nu till tjänstkatalogdiagrammet till Helm-lagringsplatsen:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Slutligen installerar du tjänstkatalogen med Helm-diagrammet. Om klustret är RBAC-aktiverat kör du det här kommandot:

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set controllerManager.healthcheck.enabled=false
```

Om klustret inte är RBAC-aktiverat kör du det här kommandot:

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false --set controllerManager.healthcheck.enabled=false
```

När Helm-diagrammet har körts verifierar du att `servicecatalog` visas i utdata från följande kommando:

```azurecli-interactive
kubectl get apiservice
```

Du kan exempelvis se utdata som liknar följande (trunkerat här):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Installera Open Service Broker for Azure

Nästa steg är att installera [Open Service Broker for Azure][open-service-broker-azure], vilket inkluderar katalogen för de Azure-hanterade tjänsterna. Exempel på tillgängliga Azure-tjänster är Azure Database for PostgreSQL, Azure Database for MySQL och Azure SQL Database.

Börja med att lägga till Open Service Broker for Azure Helm-lagringsplatsen:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Skapa [Tjänstens huvudnamn][create-service-principal] med följande Azure CLI-kommando:

```azurecli-interactive
az ad sp create-for-rbac
```

Resultatet bör likna följande. Anteckna värdena `appId`, `password` och `tenant`, som du ser i nästa steg.

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

Ange följande miljövariabler med föregående värden:

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Skaffa nu ID:t för din Azure-prenumeration:

```azurecli-interactive
az account show --query id --output tsv
```

Ange följande miljövariabler med föregående värde igen:

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

Nu när du har fyllt i de här miljövariablerna kör du följande kommando för att installera Open Service Broker for Azure med Helm-diagrammet:

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

När OSBA-distributionen är klar installerar du [CLI för tjänstkatalogen][service-catalog-cli], ett lättanvänt kommandoradsgränssnitt för att fråga service broker, tjänstklasser, tjänstplaner med mera.

Kör följande kommandon för att installera CLI-binary för tjänstkatalog:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Ange nu installerade service brokers:

```azurecli-interactive
./svcat get brokers
```

Du bör se utdata som liknar följande:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Ange nu de tillgängliga tjänstklasserna. Tjänstklasserna som visas är de tillgängliga Azure-hanterade tjänster som kan etableras via Open Service Broker for Azure.

```azurecli-interactive
./svcat get classes
```

Slutligen listar du alla tillgängliga serviceplaner. Serviceplaner är servicenivåer för Azure-hanterade tjänster. Till exempel sträcker sig planerna för Azure Database for MySQL från `basic50` för Basic-nivån med 50 Database Transaction Units (DTU:er) till `standard800` för Standard-nivån med 800 DTU:er.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Installera WordPress från Helm-diagrammet med Azure Database for MySQL

I det här steget använder du Helm för att installera ett uppdaterat Helm-diagram för WordPress. Diagrammet etablerar en extern Azure Database for MySQL-instans som WordPress kan använda. Den här processen kan ta ett par minuter.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0 --set replicaCount=1
```

För att verifiera att installationen har etablerat rätt resurser ska du ange en lista över de installerade tjänstinstanserna och bindningarna:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Ange installerade hemligheter:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Nästa steg

Genom att följa den här artikeln har du distribuerat Tjänstkatalog till ett AKS-kluster (Azure Kubernetes Service). Du har använt Open Service Broker for Azure för att distribuera en WordPress-installation som använder Azure-hanterade tjänster, i det här fallet Azure Database for MySQL.

På lagringsplatsen [Azure/helm-charts][helm-charts] kan du få åtkomst till andra uppdaterade OSBA-baserade Helm-diagram. Om du är intresserad av att skapa egna diagram som fungerar med OSBA kan du läsa [Skapa ett nytt diagram][helm-create-new-chart].

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: https://docs.helm.sh/helm/#helm-install
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
