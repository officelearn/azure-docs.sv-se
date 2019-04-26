---
title: Använda OpenFaaS med Azure Kubernetes Service (AKS)
description: Distribuera och använda OpenFaaS med Azure Kubernetes Service (AKS)
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 5ed6e0b21b00ede3f78a102fd004e5706ae3cea5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464906"
---
# <a name="using-openfaas-on-aks"></a>Använda OpenFaaS på AKS

[OpenFaaS] [ open-faas] är ett ramverk för att skapa funktioner utan server med hjälp av behållare. Den har fått storskaliga införande inom gruppen som ett projekt med öppen källkod. Det här dokumentet beskriver hur du installerar och använda OpenFaas på ett kluster i Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna slutföra stegen i den här artikeln.

* Grundläggande kunskaper om Kubernetes.
* Ett kluster i Azure Kubernetes Service (AKS) och AKS-autentiseringsuppgifter som konfigurerats i utvecklingssystemet.
* Azure CLI installerad i utvecklingssystemet.
* Git kommandoradsverktyg installeras på datorn.

## <a name="add-the-openfaas-helm-chart-repo"></a>Lägg till lagringsplatsen för OpenFaaS helm-diagram

OpenFaaS har sin egen helm-diagram för att hålla dig uppdaterad med de senaste ändringarna.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Distribuera OpenFaaS

Ett bra tips är OpenFaaS och OpenFaaS funktioner ska lagras i sin egen Kubernetes-namnområdet.

Skapa ett namnområde för OpenFaaS system och -funktioner:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Skapa ett lösenord för OpenFaaS UI-Portal och REST-API:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Du kan hämta värdet för hemligheten med `echo $PASSWORD`.

Lösenordet som vi skapar här ska användas av helm-diagrammet för att aktivera grundläggande autentisering på OpenFaaS gatewayen, som är exponerad för Internet via cloud LoadBalancer.

Ett Helm-diagram för OpenFaaS ingår i den klonade lagringsplatsen. Använd det här diagrammet för att distribuera OpenFaaS i AKS-klustret.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Utdata:

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

En offentlig IP-adress har skapats för att komma åt OpenFaaS gatewayen. Använd för att hämta den här IP-adressen, den [kubectl hämta tjänst] [ kubectl-get] kommando. Det kan ta en minut för IP-adress som ska tilldelas till tjänsten.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Utdata.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Om du vill testa systemet OpenFaaS, bläddra till den externa IP-adressen på port 8080, `http://52.186.64.52:8080` i det här exemplet. Du uppmanas att logga in. Om du vill hämta ditt lösenord, ange `echo $PASSWORD`.

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

Slutligen installera OpenFaaS CLI. Det här exemplet används brew, finns i den [OpenFaaS CLI-dokumentationen] [ open-faas-cli] fler alternativ.

```console
brew install faas-cli
```

Ange `$OPENFAAS_URL` till den offentliga IP-Adressen som hittas ovan.

Logga in med Azure CLI:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Skapa första funktion

Nu när OpenFaaS är i drift, skapar du en funktion med hjälp av OpenFaas-portalen.

Klicka på **distribuera ny funktion** och Sök efter **Figlet**. Välj funktionen Figlet och klicka på **distribuera**.

![Figlet](media/container-service-serverless/figlet.png)

Använda curl för att anropa funktionen. Ersätt IP-adressen i följande exempel med din OpenFaas gateway.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Utdata:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Skapa andra funktion

Nu ska du skapa en andra funktion. Det här exemplet kommer att distribueras med hjälp av OpenFaaS CLI och innehåller en anpassad behållaravbildning och hämta data från en Cosmos DB. Flera objekt måste konfigureras innan du skapar funktionen.

Skapa först en ny resursgrupp för Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Distribuera en CosmosDB-instans av typen `MongoDB`. Instansen måste ha ett unikt namn, uppdatera `openfaas-cosmos` till något som är unik för din miljö.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Hämta anslutningssträngen för Cosmos-databasen och lagra den i en variabel.

Uppdatera värdet för den `--resource-group` argument för namnet på resursgruppen, och `--name` argument för namnet på ditt Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Nu ska du fylla i Cosmos DB med testdata. Skapa en fil med namnet `plans.json` och kopiera följande json.

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

Om det behövs installerar du Verktyg för MongoDB. I följande exempel installeras med hjälp av brew, finns i den [MongoDB-dokumentation] [ install-mongo] för andra alternativ.

```azurecli-interactive
brew install mongodb
```

Läs in data i databasen.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Utdata:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Kör följande kommando för att skapa funktionen. Uppdatera värdet för den `-g` argumentet med din OpenFaaS gateway-adress.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

När distribuerats bör du se din nyligen skapade OpenFaaS slutpunkt för funktionen.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testa funktionen med curl. Uppdatera IP-adress med OpenFaaS gateway-adress.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Utdata:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Du kan också testa funktionen inom OpenFaaS UI.

![alternativ text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta att lära dig med OpenFaaS workshop via en uppsättning praktiska övningar som tar till exempel att skapa ditt eget GitHub-robot förbrukar hemligheter, visa mått och automatisk skalning.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
