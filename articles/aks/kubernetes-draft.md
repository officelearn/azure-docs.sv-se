---
title: Utveckla på Azure Kubernetes Service (AKS) med Draft
description: Använda Draft med AKS och Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303534"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Snabbstart: Utveckla på Azure Kubernetes Service (AKS) med Draft

Draft är ett verktyg med öppen källkod som hjälper till att paketet och kör programmet behållare i ett Kubernetes-kluster. Med Draft, du kan snabbt distribuera om ett program till Kubernetes när ändringar i koden sker utan att behöva genomföra ändringarna till versionskontrollen. Mer information om Draft finns i den [utkast för dokumentation på GitHub][draft-documentation].

Den här artikeln visar hur du använder paketet för utkast och kör ett program på AKS.


## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installerat och konfigurerat. Docker innehåller paket som konfigurerar Docker på en [Mac][docker-for-mac], [Windows][docker-for-windows], eller [Linux][docker för linux] system.
* [Helm installerat](https://github.com/helm/helm/blob/master/docs/install.md).
* [Draft installerat][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes Service-kluster

Skapa ett AKS-kluster. Den skapar en resursgrupp som heter MyResourceGroup och ett AKS-kluster som heter MyAKS kommandona nedan.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Om du vill använda Draft för att köra programmet i AKS-klustret, behöver du ett Azure Container Registry för att lagra dina behållaravbildningar. I exemplet nedan använder [az acr skapa][az-acr-create] att skapa en ACR med namnet *MyDraftACR* i den *MyResourceGroup* resursgrupp med det *grundläggande* SKU: N. Du bör ange dina egna unika registernamn. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

De utdata som genereras påminner om de i följande exempel. Anteckna den *loginServer* värde för din ACR eftersom den används i ett senare steg. I det exemplet nedan *mydraftacr.azurecr.io* är den *loginServer* för *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


För ett utkast att använda ACR-instansen, måste du först logga in. Använd den [docker login][az-acr-login] kommando för att logga in. I exemplet nedan ska logga in på en ACR med namnet *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Du får ett meddelande om att *inloggningen lyckades* när inloggningen är klar.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Skapa förtroende mellan AKS-klustret och ACR

AKS-klustret måste också åtkomst till din ACR och hämta behållaravbildningarna och kör dem. Du tillåter åtkomst till ACR från AKS genom att upprätta ett förtroende. Bevilja behörigheter för Azure Active Directory-tjänstens huvudnamn som används av AKS-klustret för att få åtkomst till ACR-registret för att upprätta förtroende mellan ett AKS-kluster och en ACR-registret. Följande kommandon för att bevilja behörigheter till tjänstens huvudnamn för den *MyAKS* -kluster i den *MyResourceGroup* till den *MyDraftACR* ACR i den  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Ansluta till ditt AKS-kluster

Om du vill ansluta till Kubernetes-klustret från din lokala dator måste du använda [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifterna för AKS-kluster med namnet *MyAKS* i den *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Skapa ett tjänstkonto för Helm

Innan du kan distribuera Helm i en RBAC-aktiverade AKS-kluster, behöver du ett tjänstkonto och rollen bindning för Tiller-tjänsten. Mer information om hur du skyddar Helm / Tiller i en RBAC aktiverat kluster, se [Tiller namnområden och RBAC][tiller-rbac]. Om AKS-klustret inte RBAC aktiverad, kan du hoppa över det här steget.

Skapa en fil med namnet `helm-rbac.yaml` och kopiera följande YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Skapa kontot och rollen bindning med den `kubectl apply` kommando:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurera Helm
För att distribuera en grundläggande Tiller i ett AKS-kluster måste använda den [”helm init”][helm-init] kommando. Om klustret inte är aktiverat RBAC, ta bort den `--service-account` argument och värde.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurera Draft

Om du inte har konfigurerat utkast på den lokala datorn, kör `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Du måste också konfigurera Draft för att använda den *loginServer* på din ACR. Följande kommando använder `draft config set` att använda `mydraftacr.azurecr.io` som ett register.

```console
draft config set registry mydraftacr.azurecr.io
```

Du har konfigurerat utkast om du vill använda din ACR och Draft kan push-överför avbildningar till din ACR. När utkastet kör ditt program i AKS-kluster, krävs inga lösenord eller hemligheter för att skicka till eller för att hämta från ACR-registret. Eftersom ett förtroende har skapats mellan AKS-klustret och din ACR sker-autentisering på nivån Azure Resource Manager med Azure Active Directory.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den här snabbstarten används [ett java-exempelprogram från utkast till GitHub-lagringsplatsen][example-java]. Klona programmet från GitHub och navigera till den `draft/examples/example-java/` directory.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Kör exempelprogrammet med Draft

Använd den `draft create` kommando för att förbereda programmet.

```console
draft create
```

Det här kommandot skapar artefakter som används för att köra programmet i ett Kubernetes-kluster. Dessa objekt innehåller en Dockerfile och ett Helm-diagram och en *draft.toml* -fil som är utkast till konfigurationsfilen.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Kör exempelprogrammet i AKS-kluster med den `draft up` kommando.

```console
draft up
```

Det här kommandot skapar Dockerfile för att skapa en behållaravbildning, skickar avbildningen till din ACR och installerar Helm-diagrammet för att starta programmet i AKS. Första gången du kör det här kommandot kan skickar och hämtar behållaravbildningen ta lite tid. När de grundläggande lager cachelagras minskar avsevärt den tid det tar att distribuera programmet.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Ansluta till löpande exempelprogrammet från din lokala dator

Testa programmet med den `draft connect` kommando.

```console
draft connect
```

Detta kommando proxyservrar en säker anslutning till Kubernetes-pod. När du är klar kan programmet nås på angiven URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Gå till programmet i en webbläsare med hjälp av den `localhost` URL: en till finns i exempelprogrammet. I exemplet ovan är URL: en `http://localhost:49804`. Stoppa anslutningen med `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Få åtkomst till programmet på internet

Föregående steg skapade en proxyanslutning till programpodden i AKS-klustret. När du utveckla och testa ditt program behöver du gör programmet tillgängligt på internet. Om du vill exponera ett program på internet, kan du skapa en Kubernetes-tjänst med en typ av [LoadBalancer][kubernetes-service-loadbalancer].

Uppdatera `charts/example-java/values.yaml` att skapa en *LoadBalancer* service. Ändra värdet för *service.type* från *ClusterIP* till *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Spara ändringarna, Stäng filen och kör `draft up` köra programmet på nytt.

```console
draft up
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. Du kan övervaka förloppet genom att använda den `kubectl get service` med den *watch* parameter:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Gå till belastningsutjämnaren av ditt program i en webbläsare med hjälp av den *extern IP-adress* att se exempelprogrammet. I exemplet ovan är den IP-Adressen `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Iterera på programmet

Du kan iterera ditt program genom att göra ändringarna lokalt och köra `draft up`.

Uppdatera meddelande som returneras på [rad 7 i src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
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

Om du vill se det uppdaterade programmet, navigera till IP-adressen för belastningsutjämnaren igen och kontrollera ändringarna visas.

## <a name="delete-the-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda den [az group delete][az-group-delete] kommandot att ta bort resursgruppen, AKS-klustret, container registry, behållaravbildningarna lagras där och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner för hur du tar bort tjänstens huvudnamn finns [AKS-tjänsten huvudnamn överväganden och borttagning av][sp-delete].

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Draft finns i Draft-dokumentationen på GitHub.

> [!div class="nextstepaction"]
> [Draft-dokumentation][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
