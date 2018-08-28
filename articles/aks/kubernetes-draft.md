---
title: Använda Draft med AKS och Azure Container Registry
description: Använda Draft med AKS och Azure Container Registry
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: faffd919e1c57f2dff21c42c2294a06eb82a2c0a
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092618"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Använda Draft med Azure Kubernetes Service (AKS)

Draft är ett verktyg med öppen källkod som hjälper till att paket och distribuera programmet behållare i ett Kubernetes-kluster, så att du kan koncentrera dig på rutinen dev - topp utveckling ”inre slingan”. Draft fungerar som koden utvecklas, men innan du arrangerar versionskontroll. Med Draft, du kan snabbt distribuera om ett program till Kubernetes kodändringar inträffar. Mer information om Draft finns i den [utkast för dokumentation på Github][draft-documentation].

Den här artikeln visar hur du använder utkast med ett Kubernetes-kluster i AKS.

## <a name="prerequisites"></a>Förutsättningar

Stegen som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster och har upprättat en `kubectl` anslutning med klustret. Om du behöver dessa objekt kan se den [AKS-Snabbstart][aks-quickstart].

Du behöver ett privat Docker-register i Azure Container Registry (ACR). Anvisningar om hur du skapar en ACR-instans finns i den [Snabbstart för Azure Container Registry][acr-quickstart].

Helm måste också installeras i AKS-klustret. Läs mer om hur du installerar och konfigurerar Helm [Helm för användning med Azure Kubernetes Service (AKS)][aks-helm].

Slutligen måste du installera [Docker](https://www.docker.com).

## <a name="install-draft"></a>Installera utkast

Draft CLI är en klient som körs i utvecklingssystemet och gör att du kan distribuera kod till ett Kubernetes-kluster. Om du vill installera CLI utkast på en Mac `brew`. Alternativ för ytterligare information finns i den [Draft installera guiden][draft-documentation].

> [!NOTE]
> Om du har installerat Draft före version 0.12 först ta bort Draft från ditt kluster med hjälp av `helm delete --purge draft` och ta sedan bort den lokala konfigurationen genom att köra `rm -rf ~/.draft`. Om du är i Mac OS, kör `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Nu initiera utkast med den `draft init` kommando:

```console
draft init
```

## <a name="configure-draft"></a>Konfigurera Draft

Draft skapar behållaravbildningar lokalt och sedan antingen distribuerar dem från det lokala registret (till exempel med Minikube), eller använder ett avbildningsregister som du anger. Den här artikeln använder Azure Container Registry (ACR), så du måste upprätta en förtroenderelation mellan AKS-klustret och ACR-registret, sedan konfigurera Draft för att skicka dina behållaravbildningar till ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Skapa förtroende mellan AKS-klustret och ACR

Bevilja behörigheter för Azure Active Directory-tjänstens huvudnamn som används av AKS-klustret för att få åtkomst till ACR-registret för att upprätta förtroende mellan ett AKS-kluster och en ACR-registret. I följande kommandon och ange dina egna `<resourceGroupName>`, Ersätt `<aksName>` med namnet på din AKS-kluster och Ersätt `<acrName>` med namnet på din ACR-registret:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Läs mer om dessa steg för att få åtkomst till ACR, [autentisera med ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Konfigurera Draft för att skicka till och distribuera från ACR

Nu när du har en förtroenderelation mellan AKS och ACR, aktivera användning av ACR från ditt AKS-kluster.

1. Konfigurera Draft *registret* värde. I följande kommandon och Ersätt `<acrName>` med namnet på din ACR-registret:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Logga in på ACR-registret med [docker login][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Som ett förtroende skapades mellan AKS och ACR, krävs inga lösenord eller hemligheter för att skicka till eller för att hämta från ACR-registret. Autentisering sker på nivån Azure Resource Manager med Azure Active Directory.

## <a name="run-an-application"></a>Kör ett program

Om du vill se hur Draft fungerar, vi distribuerar ett exempelprogram från den [Draft-lagringsplatsen][draft-repo]. Först klona lagringsplatsen:

```console
git clone https://github.com/Azure/draft
```

Ändra till katalogen examples Java:

```console
cd draft/examples/example-java/
```

Använd den `draft create` kommando för att starta processen. Det här kommandot skapar artefakter som används för att köra programmet i ett Kubernetes-kluster. Dessa objekt innehåller en Dockerfile och ett Helm-diagram och en *draft.toml* -fil som är utkast till konfigurationsfilen.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Kör exempelprogrammet i AKS-kluster med den `draft up` kommando. Det här kommandot skapar Dockerfile för att skapa en behållaravbildning, skickar avbildningen till ACR och slutligen installerar Helm-diagrammet för att starta programmet i AKS.

Första gången det här kommandot körs, kan skickar och hämtar behållaravbildningen ta lite tid. När de grundläggande lager cachelagras minskar avsevärt den tid det tar att distribuera programmet.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Om du stöter på problem med push-överför Docker-avbildningen kan du se till att du har har loggat in till ACR-registret med [docker login][az-acr-login], försök sedan den `draft up` -kommandot på nytt.

## <a name="test-the-application-locally"></a>Testa appen lokalt

Testa programmet med den `draft connect` kommando. Detta kommando proxyservrar en säker anslutning till Kubernetes-pod. När du är klar kan programmet nås på angiven URL.

> [!NOTE]
> Det kan ta några minuter för behållaravbildningen hämtas och programmet för att börja. Om du får ett fel vid åtkomst till programmet, försöka ansluta igen.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Om du vill komma åt ditt program, öppna en webbläsare till den adressen och porten som anges i den `draft connect` utdata som *http://localhost:49804*. 

![Java-exempelapp som körs med Draft](media/kubernetes-draft/sample-app.png)

Använd `Control+C` att stoppa proxyanslutningen.

> [!NOTE]
> Du kan också använda den `draft up --auto-connect` kommando för att skapa och distribuera ditt program och sedan ansluta direkt till den första behållaren som körs.

## <a name="access-the-application-on-the-internet"></a>Få åtkomst till programmet på internet

Föregående steg skapade en proxyanslutning till programpodden i AKS-klustret. När du utveckla och testa ditt program behöver du gör programmet tillgängligt på internet. Om du vill exponera ett program på internet, skapar du en Kubernetes-tjänst med en typ av [LoadBalancer][kubernetes-service-loadbalancer], eller skapa en [ingress-kontrollant] [ kubernetes-ingress]. Nu ska vi skapa en *LoadBalancer* service.

Först måste uppdatera den *values.yaml* utkast paketet för att ange att en tjänst med en typ *LoadBalancer* ska skapas:

```console
vi charts/java/values.yaml
```

Leta upp den *service.type* egenskapen och uppdatera värdet från *ClusterIP* till *LoadBalancer*, enligt följande komprimerade exempel:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Spara och stäng filen och sedan använda `draft up` köra programmet på:

```console
draft up
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. Du kan övervaka förloppet genom att använda den `kubectl get service` med den *watch* parameter:

```console
kubectl get service --watch
```

Första gången den *extern IP-adress* för tjänsten visas som *väntande*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

När EXTERNAL-IP-adressen har ändrats från *väntande* till en IP-adress, använder `Control+C` att stoppa den `kubectl` titta på processen:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Om du vill se programmet, bläddra till den externa IP-adressen för belastningsutjämnaren med `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterera på programmet

Nu när utkast har konfigurerats och programmet körs i Kubernetes, anger du för kod iteration. Varje gång som du vill testa uppdaterade code genom att köra den `draft up` kommando för att uppdatera programmet som körs.

Uppdatera Java-exempelprogrammet för att ändra texten som visas i det här exemplet. Öppna den *Hello.java* fil:

```console
vi src/main/java/helloworld/Hello.java
```

Uppdatera utdata texten ska visas, *Hello World, jag är Java i AKS!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Kör den `draft up` kommando för att distribuera om programmet:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Om du vill se det uppdaterade programmet curl IP-adressen för belastningsutjämnaren igen:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Draft finns i Draft-dokumentationen på GitHub.

> [!div class="nextstepaction"]
> [Draft-dokumentation][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
