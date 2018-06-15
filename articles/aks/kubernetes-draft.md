---
title: Använd utkast med AKS och Azure-behållaren registret
description: Använd utkast med AKS och Azure-behållaren registret
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5dfecefb6ce1d74e02c64371a864a6d3b07a2e1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257323"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Använd utkast med Azure Kubernetes Service (AKS)

Utkastet är ett verktyg med öppen källkod som hjälper till att innehålla och distribuera dessa behållare i ett Kubernetes kluster, så att du kan koncentrera dig på dev cykel--i topp utvecklingen av ”inre loop”. Utkast fungerar som koden utvecklas, men innan du genomför för versionskontroll. Med utkastet kan du snabbt distribuera ett program till Kubernetes när koden ändringar görs. Mer information om utkast finns i [utkast dokumentation på Github][draft-documentation].

Det här dokumentet beskriver med ett Kubernetes kluster på AKS utkast.

## <a name="prerequisites"></a>Förutsättningar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Om du behöver dessa objekt finns i [AKS quickstart][aks-quickstart].

Du måste också ett privat Docker-register i Azure Container registret (ACR). Anvisningar om hur du distribuerar en ACR-instans finns i [Azure Container registret Quickstart][acr-quickstart].

Helm måste också installeras i klustret AKS. Läs mer om hur du installerar helm [Använd Helm med Azure-Kubernetes (AKS)][aks-helm].

Slutligen måste du installera [Docker](https://www.docker.com).

## <a name="install-draft"></a>Installera utkast

Utkast CLI är en klient som kör i utvecklingssystemet och tillåter att du snabbt distribuera kod i ett Kubernetes kluster.

> [!NOTE]
> Om du har installerat ett utkast till före version 0,12 måste du först bort utkast från klustret med hjälp av `helm delete --purge draft` och ta sedan bort den lokala konfigurationen genom att köra `rm -rf ~/.draft`. Om du är på MacOS köra `brew upgrade draft`.

Så här installerar du utkast CLI på en Mac-Använd `brew`. Ytterligare installationsalternativ finns, [utkast installera guiden][draft-documentation].

```console
brew tap azure/draft
brew install draft
```

Nu initiera utkast med den `draft init` kommando.

```console
draft init
```

## <a name="configure-draft"></a>Konfigurera utkast

Utkast skapar behållaren-avbildningar lokalt och sedan antingen distribuerar dem från det lokala registret (för Minikube) eller måste du ange avbildningen registret för att använda. Det här exemplet används i Azure Container registret (ACR), så du måste upprätta en förtroenderelation mellan AKS klustret och ACR registret och konfigurera utkast för att vidarebefordra behållaren till ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Skapa förtroende mellan AKS klustret och ACR

För att upprätta förtroende mellan ett AKS kluster och en ACR-registret, kan du ändra de Azure Active Directory Service Prinicipal används med AKS genom att lägga till deltagarrollen till den med omfattning ACR-databasen. Gör du genom att köra följande kommandon, ersätter _&lt;aks-rg-name&gt;_ och _&lt;aks klusternamnet&gt;_ med resursgrupp och namnet på din AKS klustret och _&lt;acr-rg-numren&gt;_ och _&lt;acr-repo-name&gt;_ med resursnamnet för gruppen och databasen för din ACR databasen som du vill skapa förtroende.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Dessa steg och andra autentiseringsmekanismer att komma åt ACR [autentiseras med ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Konfigurera utkast för att push-installera och distribuera från ACR

Det finns en förtroenderelation mellan AKS och ACR, aktivera användning av ACR från AKS-kluster i följande steg.
1. Konfigurera utkast `registry` värde genom att köra `draft config set registry <registry name>.azurecr.io`, där _&lt;registrets&lt;_ är namnet på din ACR-registret.
2. Logga in på ACR registret genom att köra `az acr login -n <registry name>`.

Eftersom du är nu inloggad lokalt ACR och du har skapat en förtroenderelation med AKS och ACR, krävs inga lösenord eller hemligheter för att skicka till eller hämta från ACR till AKS. Autentisering sker på nivån Azure Resource Manager med Azure Active Directory.

## <a name="run-an-application"></a>Kör ett program

Utkast till databasen innehåller flera exempelprogram som kan användas för demo utkast. Skapa en klonad kopia av lagringsplatsen.

```console
git clone https://github.com/Azure/draft
```

Ändra till katalogen för Java-exempel.

```console
cd draft/examples/example-java/
```

Använd den `draft create` kommando för att starta processen. Detta kommando skapar artefakter som används för att köra programmet i ett Kubernetes kluster. Dessa objekt omfattar en Dockerfile ett Helm diagram och en `draft.toml` fil som är ett utkast till konfigurationsfilen.

```console
draft create
```

Resultat:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Om du vill köra programmet på ett Kubernetes kluster, använda den `draft up` kommando. Det här kommandot skapar Dockerfile om du vill skapa en avbildning av behållare, skickar bilden till ACR och slutligen installerar Helm diagrammet om du vill starta programmet i AKS.

Första gången detta körs kan push-installation och dra behållaren avbildningen ta lite tid; När de grundläggande lager cachelagras minska den tid det tar kraftigt.

```console
draft up
```

Resultat:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Testa programmet

Testa programmet med den `draft connect` kommando. Det här kommandot proxyservrar en anslutning till Kubernetes baljor så att en säker anslutning till lokalt. Programmet kan användas på den angivna Webbadressen när du är klar.

I vissa fall kan ta det några minuter för behållaren bilden som ska laddas ned och programmet för att starta. Om du får ett fel vid åtkomst till programmet försöka ansluta igen.

```console
draft connect
```

Resultat:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Nu testa programmet genom att bläddra till http://localhost:46143 (för i föregående exempel; porten kan vara olika). När du är klar att testa hur programmet `Control+C` att stoppa proxyanslutningen.

> [!NOTE]
> Du kan också använda den `draft up --auto-connect` kommando för att skapa och distribuera programmet och ansluta direkt till den första körs behållaren att göra upprepning växla även snabbare.

## <a name="expose-application"></a>Visa program

När du testar ett program i Kubernetes, kanske du vill gör programmet tillgängligt på internet. Detta kan göras med hjälp av en Kubernetes tjänst med en typ av [LoadBalancer] [ kubernetes-service-loadbalancer] eller en [ingång controller][kubernetes-ingress]. Det här dokumentet beskriver Kubernetes-tjänsten.


Först pack utkastet måste uppdateras för att ange att en tjänst med en typ `LoadBalancer` ska skapas. Om du vill göra det, uppdatera service-typen i den `values.yaml` filen.

```console
vi charts/java/values.yaml
```

Leta upp den `service.type` egenskapen och uppdatera värdet från `ClusterIP` till `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Kör `draft up` att köra programmet igen.

```console
draft up
```

Det kan ta några minuter för tjänsten för att returnera en offentlig IP-adress. Att övervaka förloppet använder den `kubectl get service` kommandot med en titta på.

```console
kubectl get service -w
```

Först den *externa IP-* för tjänsten visas som `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

När EXTERNAL-IP-adressen har ändrats från `pending` till en `IP address` använder du `Control+C` för att stoppa kubectl-övervakningsprocessen.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Bläddra till den externa IP-adressen för att visa programmet.

```console
curl 52.175.224.118
```

Resultat:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterera programmet

Nu när utkast har konfigurerats och programmet körs i Kubernetes, anger du för koden iteration. Varje gång som du vill testa uppdaterade code kan du köra den `draft up` kommando för att uppdatera programmet körs.

Uppdatera Java-programmet hello world i det här exemplet.

```console
vi src/main/java/helloworld/Hello.java
```

Uppdatera Hello World-texten.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Kör den `draft up --auto-connect` kommando för att distribuera programmet säkerhets så snart en baljor är redo att svara.

```console
draft up --auto-connect
```

Resultat

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Slutligen visa programmet för att se uppdateringarna.

```console
curl 52.175.224.118
```

Resultat:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ett utkast till dokumentationen i utkast på GitHub.

> [!div class="nextstepaction"]
> [Utkast till dokumentationen][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
