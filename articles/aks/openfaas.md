---
title: Använda OpenFaaS med Azure Kubernetes-tjänsten (AKS)
description: Distribuera och använda OpenFaaS med Azure-Kubernetes (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: b5484233c7d3d32e51098baad8c22ec51df8f0d8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260625"
---
# <a name="using-openfaas-on-aks"></a>Med hjälp av OpenFaaS på AKS

[OpenFaaS] [ open-faas] är ett ramverk för att skapa serverlösa funktioner utöver behållare. Den har fått storskaliga införande i gemenskapen som ett projekt med öppen källkod. Det här dokumentet beskriver hur du installerar och använder OpenFaas på ett kluster i Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna slutföra stegen i den här artikeln.

* Grundläggande förståelse för Kubernetes.
* Ett kluster med Azure Kubernetes Service (AKS) och AKS autentiseringsuppgifter som konfigurerats i utvecklingssystemet.
* Azure CLI är installerad i utvecklingssystemet.
* Git kommandoradsverktyg installeras på datorn.

## <a name="get-openfaas"></a>Hämta OpenFaaS

Klona lagringsplatsen för OpenFaaS-projekt att utvecklingssystemet.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Ändra till katalogen för den klonade lagringsplatsen.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Distribuera OpenFaaS

Ett bra tips är OpenFaaS och OpenFaaS funktioner ska lagras i sina egna Kubernetes namnområde.

Skapa ett namnområde för OpenFaaS system.

```azurecli-interactive
kubectl create namespace openfaas
```

Skapa ett andra namnområde för OpenFaaS funktioner.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

Ett diagram Helm OpenFaaS ingår i klonade databasen. Använd det här diagrammet för att distribuera OpenFaaS i AKS-kluster.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Resultat:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

En offentlig IP-adress har skapats för att komma åt OpenFaaS gateway. Använd för att hämta denna IP-adress i [kubectl hämta service] [ kubectl-get] kommando. Det kan ta ett tag för IP-adress tilldelas till tjänsten.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Utdata.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Om du vill testa OpenFaaS system, bläddra till den externa IP-adressen på port 8080, `http://52.186.64.52:8080` i det här exemplet.

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Slutligen installera OpenFaaS CLI. Det här exemplet används brew, finns det [OpenFaaS CLI dokumentationen] [ open-faas-cli] fler alternativ.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Skapa första funktion

Nu OpenFaaS fungerar, skapa en funktion med hjälp av OpenFaas-portalen.

Klicka på **distribuera nya funktionen** och Sök efter **Figlet**. Välj funktionen Figlet och klicka på **distribuera**.

![Figlet](media/container-service-serverless/figlet.png)

Använd curl för att anropa funktionen. Ersätt IP-adressen i följande exempel med OpenFaas-gateway.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Resultat:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Skapa andra funktion

Nu ska du skapa en annan funktion. Det här exemplet kommer att distribueras med hjälp av CLI OpenFaaS och innehåller en avbildning för anpassade container och hämta data från en Cosmos-DB. Flera objekt måste konfigureras innan du skapar funktionen.

Först skapa en ny resursgrupp för Cosmos-DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Distribuera en CosmosDB instans av typen `MongoDB`. Instansen behöver ett unikt namn, uppdatera `openfaas-cosmos` till något som är unik för din miljö.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Hämta Cosmos-anslutningssträngen för databasen och lagra den i en variabel.

Uppdatera värdet för den `--resource-group` argument för namnet på din resursgrupp och `--name` argumentet Cosmos-DB-namn.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nu fylla Cosmos-DB med testdata. Skapa en fil med namnet `plans.json` och kopiera följande json.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Använd den *mongoimport* verktyg för att läsa in CosmosDB-instans med data.

Om det behövs installerar du Verktyg för MongoDB. I följande exempel installeras med hjälp av brew, finns det [MongoDB-dokumentation] [ install-mongo] för andra alternativ.

```azurecli-interactive
brew install mongodb
```

Läs in data i databasen.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Resultat:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Kör följande kommando för att skapa funktionen. Uppdatera värdet för den `-g` argumentet med din OpenFaaS gateway.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

När har distribuerats, bör du se nyligen skapade OpenFaaS slutpunkten för funktionen.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testa funktionen med curl. Uppdatera IP-adressen med OpenFaaS gateway.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Resultat:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Du kan också testa funktionen inom OpenFaaS UI.

![alternativ text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Nästa steg

Standarddistribution av OpenFaas måste vara låst för både OpenFaaS Gateway och funktioner. [Alex Ellis blogginlägget](https://blog.alexellis.io/lock-down-openfaas/) innehåller mer information om säker konfigurationsalternativ.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
