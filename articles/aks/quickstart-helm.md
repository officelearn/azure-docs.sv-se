---
title: Utveckla på Azure Kubernetes Service (AKS) med Helm
description: Använd Helm med AKS och Azure Container Registry för att paketera och köra programbehållare i ett kluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873438"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Snabbstart: Utveckla på Azure Kubernetes Service (AKS) med Helm

[Helm][helm] är ett förpackningsverktyg med öppen källkod som hjälper dig att installera och hantera livscykeln för Kubernetes-program. I likhet med Linux-pakethanterare som *APT* och *Yum*används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du använder Helm för att paketera och köra ett program på AKS. Mer information om hur du installerar ett befintligt program med Helm finns i [Installera befintliga program med Helm i AKS][helm-existing].

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installerad och konfigurerad. Docker tillhandahåller paket som konfigurerar Docker i ett [Mac][docker-for-mac]-, [Windows][docker-for-windows]- eller [Linux][docker-for-linux]-system.
* [Helm v3 installerat][helm-install].

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Om du vill använda Helm för att köra ditt program i AKS-klustret behöver du ett Azure Container-register för att lagra behållaravbildningarna. I exemplet nedan används [az acr-skapa][az-acr-create] för att skapa en ACR med namnet *MyHelmACR* i resursgruppen *MyResourceGroup* med *basic* SKU. Du bör ange ditt eget unika registernamn. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

De utdata som genereras påminner om de i följande exempel. Anteckna *loginServer-värdet* för din ACR eftersom det kommer att användas i ett senare steg. I exemplet nedan är *myhelmacr.azurecr.io* *inloggningsservern* för *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
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

Om du vill använda ACR-instansen måste du först logga in. Använd kommandot [az acr-inloggning][az-acr-login] för att logga in. Nedanstående exempel loggas in på en ACR med namnet *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Kommandot returnerar ett meddelande om inloggning som har slutförts när det har *slutförts.*

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Skapa ett AKS-kluster. Kommandot nedan skapar ett AKS-kluster som heter MyAKS och bifogar MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

AKS-klustret behöver åtkomst till acr-enheten för att hämta behållaravbildningarna och köra dem. Kommandot ovan ger också *MyAKS-klustret* åtkomst till *din MyHelmACR* ACR.

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

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den här snabbstarten använder [ett exempel nod.js-program från exempeldatabasen Azure Dev Spaces][example-nodejs]. Klona programmet från GitHub `dev-spaces/samples/nodejs/getting-started/webfrontend` och navigera till katalogen.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Skapa en Dockerfile

Skapa en ny *Dockerfile-fil* med följande:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Skapa och skicka exempelprogrammet till ACR

Hämta inloggningsserveradressen med kommandot [az acr list][az-acr-list] och fråga efter *loginServer:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd Docker för att skapa, tagga och skicka exempelprogrambehållaren till ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Skapa helm-diagrammet

Generera Helm-diagrammet `helm create` med kommandot.

```console
helm create webfrontend
```

Gör följande uppdateringar av *webfrontend/values.yaml:*

* Ändra `image.repository` till`<acrLoginServer>/webfrontend`
* Ändra `service.type` till`LoadBalancer`

Ett exempel:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Uppdatera `appVersion` `v1` till i *webfrontend/Chart.yaml*. Exempel

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Kör ditt Helm-diagram

Använd `helm create` kommandot för att installera programmet med Helm-diagrammet.

```console
helm install webfrontend webfrontend/
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. Om du vill övervaka `kubectl get service` förloppet använder du kommandot med klockparametern: *watch*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigera till belastningsutjämnaren för `<EXTERNAL-IP>` ditt program i en webbläsare med hjälp av för att se exempelprogrammet.

## <a name="delete-the-cluster"></a>Ta bort klustret

När klustret inte längre behövs använder du kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, AKS-klustret, behållarregistret, behållaravbildningarna som lagras där och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du har använt en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Dokumentation för helm][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
