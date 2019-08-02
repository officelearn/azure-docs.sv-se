---
title: Utveckla på Azure Kubernetes service (AKS) med Draft
description: Använd Draft med AKS och Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303534"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Snabbstart: Utveckla på Azure Kubernetes service (AKS) med Draft

Draft är ett verktyg med öppen källkod som hjälper till att paketera och köra program behållare i ett Kubernetes-kluster. Med Draft kan du snabbt distribuera om ett program till Kubernetes när kod ändringar sker utan att du behöver göra ändringar i versions kontrollen. Mer information om utkast finns i [utkast dokumentationen om GitHub][draft-documentation].

Den här artikeln visar hur du använder Draft-paket och kör ett program på AKS.


## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installerat och konfigurerat. Docker innehåller paket som konfigurerar Docker på en [Mac][docker-for-mac]-, [Windows][docker-for-windows]-eller [Linux][docker-for-linux] -dator.
* [Helm installerad](https://github.com/helm/helm/blob/master/docs/install.md).
* [Utkast har installerats][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Skapa ett AKS-kluster. Kommandona nedan skapar en resurs grupp med namnet MyResourceGroup och ett AKS-kluster som kallas MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Om du vill använda Draft för att köra ditt program i ditt AKS-kluster måste du ha en Azure Container Registry för att lagra behållar avbildningarna. I exemplet nedan används [AZ ACR Create][az-acr-create] för att skapa en ACR med namnet *MyDraftACR* i resurs gruppen *MyResourceGroup* med *Basic* SKU. Du bör ange ditt eget unika register namn. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

De utdata som genereras påminner om de i följande exempel. Anteckna *namnet* -värdet för ditt ACR eftersom det kommer att användas i ett senare steg. I exemplet nedan är *Mydraftacr.azurecr.io* *namnet* för *mydraftacr*.

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


För att Draft ska kunna använda ACR-instansen måste du först logga in. Använd kommandot [AZ ACR login][az-acr-login] för att logga in. Exemplet nedan kommer att logga in på en ACR med namnet *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Du får ett meddelande om att *inloggningen lyckades* när inloggningen är klar.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Skapa förtroende mellan AKS-kluster och ACR

Ditt AKS-kluster behöver också åtkomst till din ACR för att hämta behållar avbildningarna och köra dem. Du tillåter åtkomst till ACR från AKS genom att upprätta ett förtroende. Om du vill upprätta förtroende mellan ett AKS-kluster och ett ACR-register beviljar du behörigheter för Azure Active Directory tjänstens huvud namn som används av AKS-klustret för att komma åt ACR-registret. Följande kommandon ger behörighet till tjänstens huvud namn för *MyAKS* -klustret i *MyResourceGroup* till *MyDraftACR* -ACR i *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Anslut till ditt AKS-kluster

Om du vill ansluta till Kubernetes-klustret från den lokala datorn använder du [kubectl][kubectl], Kubernetes kommando rads klient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifter för AKS-klustret med namnet *MyAKS* i *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Skapa ett tjänst konto för Helm

Innan du kan distribuera Helm i ett RBAC-aktiverat AKS-kluster behöver du ett tjänst konto och en roll bindning för till-tjänsten. Mer information om hur du skyddar Helm/till i ett RBAC-aktiverat kluster finns i [till exempel, namnrymder och RBAC][tiller-rbac]. Om ditt AKS-kluster inte är RBAC-aktiverat, hoppar du över det här steget.

Skapa en fil med `helm-rbac.yaml` namnet och kopiera i följande yaml:

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

Skapa tjänst kontot och roll bindningen med `kubectl apply` kommandot:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurera Helm
Om du vill distribuera en Basic-till-till-AKS-kluster använder du kommandot [Helm init][helm-init] . Om klustret inte är RBAC-aktiverat tar du `--service-account` bort argumentet och värdet.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurera utkast

Om du inte har konfigurerat utkast på den lokala datorn kör `draft init`du:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Du måste också konfigurera Draft för att använda *namnet* för din ACR. Följande kommando används `draft config set` för att använda `mydraftacr.azurecr.io` som ett register.

```console
draft config set registry mydraftacr.azurecr.io
```

Du har konfigurerat utkastet till att använda din ACR, och utkastet kan skicka behållar avbildningar till din ACR. När Draft kör ditt program i ditt AKS-kluster krävs inga lösen ord eller hemligheter för att skicka eller hämta från ACR-registret. Eftersom ett förtroende skapades mellan ditt AKS-kluster och din ACR, sker autentiseringen på Azure Resource Manager nivå med Azure Active Directory.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den här snabb starten använder [ett exempel på ett Java-program från utkastet GitHub][example-java]-lagringsplatsen. Klona programmet från GitHub och navigera till `draft/examples/example-java/` katalogen.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Kör exempel programmet med utkast

`draft create` Använd kommandot för att förbereda programmet.

```console
draft create
```

Det här kommandot skapar de artefakter som används för att köra programmet i ett Kubernetes-kluster. Dessa objekt omfattar ett Dockerfile, ett Helm-diagram och en *Draft. toml* -fil, som är utkast konfigurations filen.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Om du vill köra exempel programmet i AKS-klustret använder du `draft up` kommandot.

```console
draft up
```

Det här kommandot skapar en Dockerfile för att skapa en behållar avbildning, push-överför avbildningen till din ACR och installerar Helm-diagrammet för att starta programmet i AKS. Första gången du kör det här kommandot kan det ta lite tid att skicka och hämta behållar avbildningen. När bas lagren har cachelagrats minskas tiden för att distribuera programmet dramatiskt.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Ansluta till exempel programmet som körs från den lokala datorn

Om du vill testa programmet använder du `draft connect` kommandot.

```console
draft connect
```

Detta kommando proxyservrar en säker anslutning till Kubernetes-pod. När du är klar kan du komma åt programmet på den angivna URL: en.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Navigera till programmet i en webbläsare med hjälp `localhost` av URL: en för att se exempel programmet. I exemplet ovan är `http://localhost:49804`URL: en. Stoppa anslutningen med `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Öppna programmet på Internet

Föregående steg skapade en proxyanslutningar till programmet Pod i ditt AKS-kluster. När du utvecklar och testar ditt program kanske du vill göra programmet tillgängligt på Internet. För att exponera ett program på Internet kan du skapa en Kubernetes-tjänst med en typ av [Loadbalancer][kubernetes-service-loadbalancer].

Uppdatera `charts/example-java/values.yaml` för att skapa en *Loadbalancer* -tjänst. Ändra värdet för *service. Type* från *ClusterIP* till *Loadbalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Spara ändringarna, Stäng filen och kör `draft up` sedan programmet igen.

```console
draft up
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. För att övervaka förloppet använder du `kubectl get service` kommandot med parametern *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Gå till belastningsutjämnare för ditt program i en webbläsare med hjälp av den *externa-IP-adressen* för att se exempel programmet. I exemplet ovan är `52.175.224.118`IP-adressen.

## <a name="iterate-on-the-application"></a>Iterera i programmet

Du kan iterera ditt program genom att göra ändringar lokalt och köra `draft up`igen.

Uppdatera meddelandet som returnerades på [rad 7 i src/main/Java/HelloWorld/Hej. java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

`draft up` Kör kommandot för att distribuera om programmet:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Om du vill se det uppdaterade programmet navigerar du till IP-adressen för belastningsutjämnaren igen och kontrollerar att ändringarna visas.

## <a name="delete-the-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, AKS-klustret, behållar registret, behållar avbildningarna som lagras där och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Anvisningar om hur du tar bort tjänstens huvud namn finns i [AKS Service Principal överväganden och borttagning][sp-delete].

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Draft finns i utkast dokumentationen på GitHub.

> [!div class="nextstepaction"]
> [Utkast dokumentation][draft-documentation]


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
