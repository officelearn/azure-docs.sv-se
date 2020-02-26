---
title: Använda OpenFaaS med Azure Kubernetes service (AKS)
description: Distribuera och använda OpenFaaS med Azure Kubernetes service (AKS)
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: e684aee1469f855ec651567b805262c71aaf32e5
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594931"
---
# <a name="using-openfaas-on-aks"></a>Använda OpenFaaS på AKS

[OpenFaaS][open-faas] är ett ramverk för att skapa Server funktioner med hjälp av behållare. Som ett projekt med öppen källkod har det fått stor skala i communityn. Det här dokumentet innehåller information om hur du installerar och använder OpenFaas i ett AKS-kluster (Azure Kubernetes service).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra stegen i den här artikeln behöver du följande.

* Grundläggande förståelse för Kubernetes.
* Ett Azure Kubernetes service-kluster (AKS) och AKS-autentiseringsuppgifter har kon figurer ATS i utvecklings systemet.
* Azure CLI installerat i utvecklings systemet.
* Git kommando rads verktyg som är installerade på systemet.

## <a name="add-the-openfaas-helm-chart-repo"></a>Lägg till OpenFaaS Helm-diagrammets lagrings platsen

OpenFaaS underhåller sina egna Helm-diagram för att hålla dig uppdaterad med alla de senaste ändringarna.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Distribuera OpenFaaS

Som en bra idé bör OpenFaaS-och OpenFaaS-funktioner lagras i sitt eget Kubernetes-namnområde.

Skapa ett namn område för OpenFaaS-systemet och-funktioner:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Skapa ett lösen ord för OpenFaaS-användargränssnitt-portalen och REST API:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Du kan hämta värdet för hemligheten med `echo $PASSWORD`.

Lösen ordet som vi skapar här används av Helm-diagrammet för att aktivera grundläggande autentisering på OpenFaaS-gatewayen, som exponeras för Internet via en molnbaserad belastningsutjämnare.

Ett Helm-diagram för OpenFaaS ingår i den klonade lagrings platsen. Använd det här diagrammet för att distribuera OpenFaaS till ditt AKS-kluster.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
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

En offentlig IP-adress har skapats för åtkomst till OpenFaaS-gatewayen. Använd kommandot [kubectl get service][kubectl-get] för att hämta den här IP-adressen. Det kan ta en stund innan IP-adressen har tilldelats till tjänsten.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Utdataparametrar.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Du testar OpenFaaS-systemet genom att bläddra till den externa IP-adressen på Port 8080 `http://52.186.64.52:8080` i det här exemplet. Du uppmanas att logga in. Ange `echo $PASSWORD`för att hämta ditt lösen ord.

![OpenFaaS-gränssnitt](media/container-service-serverless/openfaas.png)

Slutligen installerar du OpenFaaS CLI. I det här exemplet används Brew finns mer alternativ i [OPENFAAS CLI-dokumentationen][open-faas-cli] .

```console
brew install faas-cli
```

Ange `$OPENFAAS_URL` till den offentliga IP som finns ovan.

Logga in med Azure CLI:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Skapa första funktionen

Nu när OpenFaaS fungerar skapar du en funktion med hjälp av OpenFaas-portalen.

Klicka på **distribuera ny funktion** och Sök efter **figlet**. Välj funktionen figlet och klicka på **distribuera**.

![Figlet](media/container-service-serverless/figlet.png)

Använd sväng för att anropa funktionen. Ersätt IP-adressen i följande exempel med den för din OpenFaas-Gateway.

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

## <a name="create-second-function"></a>Skapa den andra funktionen

Nu ska du skapa en andra funktion. Det här exemplet kommer att distribueras med hjälp av OpenFaaS CLI och innehåller en anpassad behållar avbildning och hämta data från en Cosmos DB. Flera objekt måste konfigureras innan funktionen skapas.

Skapa först en ny resurs grupp för Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Distribuera en CosmosDB-instans av typen `MongoDB`. Instansen måste ha ett unikt namn, uppdatera `openfaas-cosmos` till något som är unikt för din miljö.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Hämta anslutnings strängen för Cosmos-databasen och lagra den i en variabel.

Uppdatera värdet för argumentet `--resource-group` till namnet på din resurs grupp och argumentet `--name` till namnet på din Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Fyll nu i Cosmos DB med test data. Skapa en fil med namnet `plans.json` och kopiera i följande JSON.

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

Använd *mongoimport* -verktyget för att läsa in CosmosDB-instansen med data.

Om det behövs installerar du MongoDB-verktygen. I följande exempel installeras dessa verktyg med hjälp av Brew i [MongoDB-dokumentationen][install-mongo] för andra alternativ.

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

Kör följande kommando för att skapa funktionen. Uppdatera värdet för argumentet `-g` med din OpenFaaS-Gateway-adress.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

När du har distribuerat bör du se den nyligen skapade OpenFaaS-slutpunkten för funktionen.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testa funktionen med hjälp av sväng. Uppdatera IP-adressen med OpenFaaS gateway-adressen.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Resultat:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Du kan också testa funktionen i OpenFaaS-ANVÄNDARGRÄNSSNITTET.

![alternativ text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta att lära dig med OpenFaaS-workshopen genom en uppsättning praktiska labb övningar som beskriver ämnen, till exempel hur du skapar din egen GitHub-robot, använder hemligheter, visar mått och automatisk skalning.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
