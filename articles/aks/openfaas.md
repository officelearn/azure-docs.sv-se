---
title: Använda OpenFaaS med Azure Kubernetes Service (AKS)
description: Distribuera och använda OpenFaaS med Azure Kubernetes Service (AKS)
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 2605489f73063cb16a588d4714955704482327ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473650"
---
# <a name="using-openfaas-on-aks"></a>Använda OpenFaaS på AKS

[OpenFaaS][open-faas] är ett ramverk för att bygga serverlösa funktioner med hjälp av behållare. Som ett open source-projekt har det fått storskaliga adoptioner inom samhället. Den här dokumentinformationen om hur du installerar och använder OpenFaas i ett AKS-kluster (Azure Kubernetes Service).

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här artikeln behöver du följande.

* Grundläggande förståelse av Kubernetes.
* Ett AKS-kluster (Azure Kubernetes Service) och AKS-autentiseringsuppgifter som konfigurerats på utvecklingssystemet.
* Azure CLI installerat på ditt utvecklingssystem.
* Git kommandoradsverktyg installerade på datorn.

## <a name="add-the-openfaas-helm-chart-repo"></a>Lägga till openfaas-helm-listan

Gå [https://shell.azure.com](https://shell.azure.com) till för att öppna Azure Cloud Shell i din webbläsare.

OpenFaaS har egna helm-diagram för att hålla sig uppdaterade med alla de senaste ändringarna.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Distribuera OpenFaaS

Som en god praxis bör OpenFaaS- och OpenFaaS-funktioner lagras i sitt eget Kubernetes-namnområde.

Skapa ett namnområde för OpenFaaS-systemet och -funktionerna:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generera ett lösenord för OpenFaaS UI Portal och REST API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Du kan få värdet av `echo $PASSWORD`hemligheten med .

Lösenordet vi skapar här kommer att användas av rodret diagrammet för att möjliggöra grundläggande autentisering på OpenFaaS Gateway, som utsätts för Internet via ett moln LoadBalancer.

Ett Helm-diagram för OpenFaaS ingår i den klonade databasen. Använd det här diagrammet om du vill distribuera OpenFaaS till AKS-klustret.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Resultat:

```output
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

En offentlig IP-adress skapas för åtkomst till OpenFaaS-gatewayen. Om du vill hämta den här IP-adressen använder du kommandot [kubectl get service.][kubectl-get] Det kan ta en minut innan IP-adressen tilldelas tjänsten.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Produktionen.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Om du vill testa OpenFaaS-systemet bläddrar du till den `http://52.186.64.52:8080` externa IP-adressen på port 8080 i det här exemplet. Du kommer att bli ombedd att logga in. Om du vill `echo $PASSWORD`hämta ditt lösenord anger du .

![OpenFaaS-användargränssnitt](media/container-service-serverless/openfaas.png)

Installera slutligen OpenFaaS CLI. Det här exemplet används brygga, se [OpenFaaS CLI-dokumentationen][open-faas-cli] för fler alternativ.

```console
brew install faas-cli
```

Ställ `$OPENFAAS_URL` in på den offentliga IP som finns ovan.

Logga in med Azure CLI:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Skapa första funktionen

Nu när OpenFaaS är i drift skapar du en funktion med OpenFaas-portalen.

Klicka på **Distribuera ny funktion** och sök efter **Figlet**. Välj funktionen Figlet och klicka på **Distribuera**.

![Figlet](media/container-service-serverless/figlet.png)

Använd curl för att anropa funktionen. Ersätt IP-adressen i följande exempel med den för din OpenFaas-gateway.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Resultat:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Skapa andra funktionen

Skapa nu en andra funktion. Det här exemplet distribueras med OpenFaaS CLI och innehåller en anpassad behållaravbildning och hämtar data från en Cosmos DB. Flera objekt måste konfigureras innan funktionen skapas.

Skapa först en ny resursgrupp för Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Distribuera en CosmosDB-instans av slag `MongoDB`. Instansen behöver ett unikt `openfaas-cosmos` namn, uppdatera till något som är unikt för din miljö.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Hämta Cosmos-databasanslutningssträngen och lagra den i en variabel.

Uppdatera värdet för `--resource-group` argumentet till namnet på resursgruppen `--name` och argumentet till namnet på cosmos-DB:n.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Fyll nu i Cosmos DB med testdata. Skapa en `plans.json` fil med namnet och kopiera i följande json.

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

Använd *mongoimport-verktyget* för att läsa in CosmosDB-instansen med data.

Installera MongoDB-verktygen om det behövs. I följande exempel installeras dessa verktyg med bryggning, se [MongoDB-dokumentationen][install-mongo] för andra alternativ.

```console
brew install mongodb
```

Läs in data i databasen.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Resultat:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Kör följande kommando för att skapa funktionen. Uppdatera värdet för `-g` argumentet med din OpenFaaS-gatewayadress.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

När du har distribuerats bör du se din nyligen skapade OpenFaaS-slutpunkt för funktionen.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testa funktionen med hjälp av lock. Uppdatera IP-adressen med OpenFaaS gateway-adressen.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Resultat:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Du kan också testa funktionen i OpenFaaS-användargränssnittet.

![alternativ text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Efterföljande moment

Du kan fortsätta att lära dig med OpenFaaS-workshopen genom en uppsättning praktiska övningar som täcker ämnen som hur du skapar din egen GitHub-robot, konsumerar hemligheter, visar mätvärden och automatisk skalning.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
