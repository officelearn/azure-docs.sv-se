---
title: Utveckla på Azure Kubernetes service (AKS) med Helm
description: Använd Helm med AKS och Azure Container Registry för att paketera och köra program behållare i ett kluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 1f67605918e093e9ab28aa88be777d27acd831ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82169576"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Snabb start: utveckla på Azure Kubernetes service (AKS) med Helm

[Helm][helm] är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. Precis som Linux-paket hanterare som *apt* och *yum*, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du använder Helm för att paketera och köra ett program på AKS. Mer information om hur du installerar ett befintligt program med hjälp av Helm finns [i installera befintliga program med Helm i AKS][helm-existing].

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installerat och konfigurerat. Docker tillhandahåller paket som konfigurerar Docker i ett [Mac][docker-for-mac]-, [Windows][docker-for-windows]- eller [Linux][docker-for-linux]-system.
* [Helm v3 installerat][helm-install].

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Om du vill använda Helm för att köra ditt program i ditt AKS-kluster måste du ha en Azure Container Registry för att lagra behållar avbildningarna. I exemplet nedan används [AZ ACR Create][az-acr-create] för att skapa en ACR med namnet *MyHelmACR* i resurs gruppen *MyResourceGroup* med *Basic* SKU. Du bör ange ditt eget unika register namn. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

De utdata som genereras påminner om de i följande exempel. Anteckna *namnet* -värdet för ditt ACR eftersom det kommer att användas i ett senare steg. I exemplet nedan är *Myhelmacr.azurecr.io* *namnet* för *myhelmacr*.

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

Om du vill använda ACR-instansen måste du först logga in. Använd kommandot [AZ ACR login][az-acr-login] för att logga in. Exemplet nedan kommer att logga in på en ACR med namnet *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Kommandot returnerar ett meddelande om att *inloggningen har* slutförts.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Skapa ett AKS-kluster. Kommandot nedan skapar ett AKS-kluster med namnet MyAKS och bifogar MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Ditt AKS-kluster behöver åtkomst till din ACR för att hämta behållar avbildningarna och köra dem. Kommandot ovan ger också *MyAKS* -klustret åtkomst till *MyHelmACR* -ACR.

## <a name="connect-to-your-aks-cluster"></a>Anslut till ditt AKS-kluster

När du ska ansluta till Kubernetes-klustret från din lokala dator använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifter för AKS-klustret med namnet *MyAKS* i *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

I den här snabb starten används [ett exempel Node.js program från Azure dev Spaces-lagringsplatsen][example-nodejs]. Klona programmet från GitHub och navigera till `dev-spaces/samples/nodejs/getting-started/webfrontend` katalogen.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Skapa en Dockerfile

Skapa en ny *Dockerfile* -fil med hjälp av följande:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Bygg och skicka exempel programmet till ACR

Hämta inloggnings serverns adress med kommandot [AZ ACR List][az-acr-list] och fråga efter *namnet*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd Docker för att bygga, tagga och skicka din exempel program behållare till ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Skapa ditt Helm-diagram

Generera ditt Helm-diagram med `helm create` kommandot.

```console
helm create webfrontend
```

Gör följande uppdateringar för *webfrontend/Values. yaml*:

* Ändra `image.repository` till`<acrLoginServer>/webfrontend`
* Ändra `service.type` till`LoadBalancer`

Till exempel:

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

Uppdatera `appVersion` till `v1` i *webfrontend/Chart. yaml*. Till exempel

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Kör ditt Helm-diagram

Använd `helm install` kommandot för att installera programmet med Helm-diagrammet.

```console
helm install webfrontend webfrontend/
```

Det tar några minuter för tjänsten att returnera en offentlig IP-adress. För att övervaka förloppet använder du `kubectl get service` kommandot med parametern *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Gå till belastningsutjämnare för ditt program i en webbläsare med hjälp av `<EXTERNAL-IP>` för att se exempel programmet.

## <a name="delete-the-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, AKS-klustret, behållar registret, behållar avbildningarna som lagras där och alla relaterade resurser.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Dokumentation om Helm][helm-documentation]

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
