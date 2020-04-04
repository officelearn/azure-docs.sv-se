---
title: Utveckla på Azure Kubernetes Service (AKS) med utkast
description: Använd Utkast med AKS och Azure Container Registry för att paketera och köra programbehållare i ett kluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 820af2d8ddf03997eea559fbc5270e84f30a805a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632883"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Snabbstart: Utveckla på Azure Kubernetes Service (AKS) med utkast

Utkast är ett verktyg med öppen källkod som hjälper till att paketera och köra programbehållare i ett Kubernetes-kluster. Med Utkast kan du snabbt distribuera om ett program till Kubernetes när kodändringar sker utan att behöva göra ändringarna i versionskontrollen. Mer information om Utkast finns i [utkastdokumentationen på GitHub][draft-documentation].

Den här artikeln visar hur du använder Utkast för att paketera och köra ett program på AKS.


## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installerad och konfigurerad. Docker tillhandahåller paket som konfigurerar Docker i ett [Mac][docker-for-mac]-, [Windows][docker-for-windows]- eller [Linux][docker-for-linux]-system.
* [Helm v2 installerat][helm-install].
* [Utkast installerat][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Skapa ett AKS-kluster. Kommandona nedan skapar en resursgrupp som heter MyResourceGroup och ett AKS-kluster som heter MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Om du vill använda Utkast för att köra ditt program i AKS-klustret behöver du ett Azure Container-register för att lagra behållaravbildningarna. I exemplet nedan används [az acr-skapa][az-acr-create] för att skapa en ACR med namnet *MyDraftACR* i resursgruppen *MyResourceGroup* med *basic* SKU. Du bör ange ditt eget unika registernamn. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

De utdata som genereras påminner om de i följande exempel. Anteckna *loginServer-värdet* för din ACR eftersom det kommer att användas i ett senare steg. I exemplet nedan är *mydraftacr.azurecr.io* *loginServer* för *MyDraftACR*.

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


För att draftas för att ACR-instansen ska kunna användas måste du först logga in. Använd kommandot [az acr-inloggning][az-acr-login] för att logga in. I exemplet nedan loggas in på en ACR med namnet *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Kommandot returnerar ett meddelande om inloggning som har slutförts när det har *slutförts.*

## <a name="create-trust-between-aks-cluster-and-acr"></a>Skapa förtroende mellan AKS-kluster och ACR

Aks-klustret behöver också åtkomst till acr-enheten för att hämta behållaravbildningarna och köra dem. Du tillåter åtkomst till ACR från AKS genom att upprätta ett förtroende. Om du vill skapa förtroende mellan ett AKS-kluster och ett ACR-register beviljar du behörigheter för azure Active Directory-tjänsthuvudnamn som används av AKS-klustret för att komma åt ACR-registret. Följande kommandon ger behörigheter till tjänstens huvudnamn för *MyAKS-klustret* i *MyResourceGroup* till *MyDraftACR* ACR i *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Ansluta till AKS-klustret

När du ska ansluta till Kubernetes-klustret från din lokala dator använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtar du autentiseringsuppgifter för AKS-klustret *MyAKS* i *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Skapa ett tjänstkonto för Helm

Innan du kan distribuera Helm i ett RBAC-aktiverat AKS-kluster behöver du ett tjänstkonto och rollbindning för Tiller-tjänsten. Mer information om hur du skyddar Helm/Tiller i ett RBAC-aktiverat kluster finns i [Tiller, Namespaces och RBAC][tiller-rbac]. Om AKS-klustret inte är RBAC aktiverat hoppar du över det här steget.

Skapa en `helm-rbac.yaml` fil med namnet och kopiera i följande YAML:

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

Skapa tjänstkontot och rollbindningen `kubectl apply` med kommandot:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurera Helm
Om du vill distribuera en grundläggande tiller i ett AKS-kluster använder du kommandot [helm init.][helm-init] Om klustret inte är aktiverat för `--service-account` RBAC tar du bort argumentet och värdet.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurera utkast

Om du inte har konfigurerat Utkast på `draft init`den lokala datorn kör du:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Du måste också konfigurera Utkast för att använda *inloggningsservern* för din ACR. Följande kommando `draft config set` används `mydraftacr.azurecr.io` för att använda som ett register.

```console
draft config set registry mydraftacr.azurecr.io
```

Du har konfigurerat Utkast för att använda ACR och Utkast kan skicka behållaravbildningar till acr-enheten. När utkast körs ditt program i AKS-klustret krävs inga lösenord eller hemligheter för att skicka till eller hämta från ACR-registret. Eftersom ett förtroende skapades mellan AKS-klustret och din ACR sker autentisering på Azure Resource Manager-nivå med Hjälp av Azure Active Directory.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den här snabbstarten använder [ett javaprogram från Draft GitHub-databasen][example-java]. Klona programmet från GitHub `draft/examples/example-java/` och navigera till katalogen.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Köra exempelprogrammet med Utkast

Använd `draft create` kommandot för att förbereda programmet.

```console
draft create
```

Det här kommandot skapar de artefakter som används för att köra programmet i ett Kubernetes-kluster. Dessa objekt inkluderar en Dockerfile, ett Helm-diagram och en *draft.toml-fil,* som är konfigurationsfilen Utkast.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Om du vill köra exempelprogrammet i `draft up` AKS-klustret använder du kommandot.

```console
draft up
```

Det här kommandot skapar Dockerfile för att skapa en behållaravbildning, skickar avbildningen till acr-filen och installerar Helm-diagrammet för att starta programmet i AKS. Första gången du kör det här kommandot kan det ta lite tid att trycka och dra behållaravbildningen. När baslagren har cachelagrats minskar den tid det tar att distribuera programmet dramatiskt.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Ansluta till exempelprogrammet som körs från den lokala datorn

Testa programmet genom att `draft connect` använda kommandot.

```console
draft connect
```

Det här kommandot proxys en säker anslutning till Kubernetes pod. När det är klart kan programmet nås på den angivna webbadressen.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Navigera till programmet i en `localhost` webbläsare med url:en för att se exempelprogrammet. I exemplet ovan är `http://localhost:49804`webbadressen . Stoppa anslutningen `Ctrl+c`med .

## <a name="access-the-application-on-the-internet"></a>Få tillgång till programmet på internet

Föregående steg skapade en proxyanslutning till programpodden i AKS-klustret. När du utvecklar och testar ditt program kanske du vill göra programmet tillgängligt på internet. Om du vill visa ett program på internet kan du skapa en Kubernetes-tjänst med en typ av [LoadBalancer][kubernetes-service-loadbalancer].

Uppdatera `charts/example-java/values.yaml` för att skapa en *LoadBalancer-tjänst.* Ändra värdet för *service.type* från *ClusterIP* till *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Spara ändringarna, stäng filen och `draft up` kör för att köra programmet igen.

```console
draft up
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. Om du vill övervaka `kubectl get service` förloppet använder du kommandot med klockparametern: *watch*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navigera till belastningsutjämnaren för ditt program i en webbläsare med hjälp av *EXTERN-IP* för att se exempelprogrammet. I exemplet ovan är `52.175.224.118`IP.

## <a name="iterate-on-the-application"></a>Iterera på ansökan

Du kan iterera programmet genom att göra `draft up`ändringar lokalt och köra om .

Uppdatera meddelandet returneras på [rad 7 av src / main/java/ helloworld / Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Kör `draft up` kommandot för att distribuera om programmet:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Om du vill se det uppdaterade programmet navigerar du till belastningsutjämningens IP-adress igen och kontrollerar att ändringarna visas.

## <a name="delete-the-cluster"></a>Ta bort klustret

När klustret inte längre behövs använder du kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, AKS-klustret, behållarregistret, behållaravbildningarna som lagras där och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Utkast finns i utkastdokumentationen på GitHub.

> [!div class="nextstepaction"]
> [Utkast till dokumentation][draft-documentation]


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
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
