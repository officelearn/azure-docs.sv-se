---
title: "Använd utkast med AKS och Azure-behållaren registret"
description: "Använd utkast med AKS och Azure-behållaren registret"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df5614d8a708b49ee1368c4d7983f45d29920fd8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Använd utkast med Azure Container Service (AKS)

Utkastet är ett verktyg med öppen källkod som hjälper till att paketet och köra kod i ett Kubernetes kluster. Utkast är inriktad på iteration utvecklingscykeln; När koden utvecklas, men innan du genomför för versionskontroll. Med utkastet kan du snabbt distribuera ett program till Kubernetes när koden ändringar görs. Mer information om utkast finns i [utkast dokumentation på Github](https://github.com/Azure/draft/tree/master/docs).

Det här dokumentet beskriver med ett Kubernetes kluster på AKS utkast.

## <a name="prerequisites"></a>Krav

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Om du behöver dessa objekt finns i [AKS quickstart](./kubernetes-walkthrough.md).

Du måste också ett privat Docker-register i Azure Container registret (ACR). Anvisningar om hur du distribuerar en ACR-instans finns i [Azure Container registret Quickstart](../container-registry/container-registry-get-started-azure-cli.md).

## <a name="install-helm"></a>Installera Helm

Helm CLI är en klient som körs på utvecklingssystemet och gör att du kan starta, stoppa och hantera program med Helm diagram.

Installera Helm CLI på en Mac med `brew`. Ytterligare installationsalternativ, se [installerar Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md).

```console
brew install kubernetes-helm
```

Resultat:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Installera utkast

Utkast CLI är en klient som kör i utvecklingssystemet och tillåter att du snabbt distribuera kod i ett Kubernetes kluster.

Så här installerar du utkast CLI på en Mac-Använd `brew`. Ytterligare installationsalternativ finns, [utkast installera guiden](https://github.com/Azure/draft/blob/master/docs/install.md).

```console
brew install draft
```

Resultat:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Konfigurera utkast

När du konfigurerar ett utkast till måste en behållare registret anges. I det här exemplet används Azure Container registret.

Kör följande kommando för att hämta namn och logga in servernamnet för din ACR-instans. Uppdatera kommandot med namnet på resursgruppen som innehåller din ACR-instans.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

ACR instans lösenordet krävs också.

Kör följande kommando för att returnera ACR-lösenord. Uppdatera kommandot med namnet på ACR-instans.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

Initiera utkast med den `draft init` kommando.

```console
draft init
```

Under den här processen tillfrågas om autentiseringsuppgifter för behållaren registret. När du använder ett Azure Container registret, registret-URL: en är ACR server inloggningsnamnet, användarnamnet är namnet på ACR-instansen och lösenordet är ACR lösenordet.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

När du är klar, är konfigurerad i kluster Kubernetes utkast och är redo att användas.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Köra ett program

Utkast till databasen innehåller flera exempelprogram som kan användas för demo utkast. Skapa en klonad kopia av lagringsplatsen.

```console
git clone https://github.com/Azure/draft
```

Ändra till katalogen för Java-exempel.

```console
cd draft/examples/java/
```

Använd den `draft create` kommando för att starta processen. Detta kommando skapar artefakter som används för att köra programmet i ett Kubernetes kluster. Dessa objekt omfattar en Dockerfile ett Helm diagram och en `draft.toml` fil som är ett utkast till konfigurationsfilen.

```console
draft create
```

Resultat:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Om du vill köra programmet på ett Kubernetes kluster, använda den `draft up` kommando. Det här kommandot filöverföringar programfiler koden och konfigurationen i Kubernetes-klustret. Den sedan körs Dockerfile för att skapa en avbildning av behållare, skickar bilden till behållaren registret och kör slutligen Helm diagrammet om du vill starta programmet.

```console
draft up
```

Resultat:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Testa programmet

Testa programmet med den `draft connect` kommando. Det här kommandot proxyservrar en anslutning till Kubernetes baljor så att en säker anslutning till lokalt. Programmet kan användas på den angivna Webbadressen när du är klar.

I vissa fall kan ta det några minuter för behållaren bilden som ska laddas ned och programmet för att starta. Om du får ett fel vid åtkomst till programmet försöka ansluta igen.

```console
draft connect
```

Resultat:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

När du är klar att testa hur programmet `Control+C` att stoppa proxyanslutningen.

## <a name="expose-application"></a>Visa program

När du testar ett program i Kubernetes, kanske du vill gör programmet tillgängligt på internet. Detta kan göras med hjälp av en Kubernetes tjänst med en typ av [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) eller en [ingång controller](https://kubernetes.io/docs/concepts/services-networking/ingress/). Det här dokumentet beskriver Kubernetes-tjänsten.


Först pack utkastet måste uppdateras för att ange att en tjänst med en typ `LoadBalancer` ska skapas. Om du vill göra det, uppdatera service-typen i den `values.yaml` filen.

```console
vi chart/java/values.yaml
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
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

När den extern IP-adressen har ändrats från `pending` till en `IP address`, Använd `Control+C` att stoppa kubectl titta på processen.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Bläddra till den externa IP-adressen om du vill se programmet.

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
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Kör den `draft up` kommando för att distribuera programmet.

```console
draft up
```

Resultat

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Slutligen visa programmet för att se uppdateringarna.

```console
curl 52.175.224.118
```

Resultat:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ett utkast till dokumentationen i utkast på GitHub.

> [!div class="nextstepaction"]
> [Utkast till dokumentationen](https://github.com/Azure/draft/tree/master/docs)