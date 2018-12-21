---
title: (INAKTUELL) Snabbstart – Azure Kubernetes-kluster för Linux
description: Lär dig snabbt att skapa ett Kubernetes-kluster på Linux-behållare i Azure Container Service med Azure CLI.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 5c5320a541e189043fc27f5c4bcd29afd5d7c70f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994150"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-linux-containers"></a>(INAKTUELL) Snabbstart – Distribuera Kubernetes-kluster för Linux-containrar

> [!TIP]
> Den uppdaterade versionen av den här snabbstarten som använder Azure Kubernetes Service finns i [Snabbstart: Distribuera ett AKS-kluster (Azure Kubernetes Service)](../../aks/kubernetes-walkthrough.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

I den här snabbstartsguiden ska vi distribuera ett Kubernetes-kluster med hjälp av Azure CLI. Därefter distribuerar vi och kör ett flerbehållarprogram som består av en webbklientdel och en Redis-instans i klustret. När vi har gjort det kan programmet nås via Internet. 

Exempelprogrammet som används i det här dokumentet är skrivet i Python. Begreppen och anvisningarna här kan användas för att distribuera valfri containeravbildning till ett Kubernetes-kluster. Koden, Dockerfile, och befintliga Kubernetes-manifestfiler som hör till projektet, finns på [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

I den här snabbstartsguiden förutsätter vi att du har grundläggande kunskaper om vanliga Kubernetes-begrepp. Detaljerad information om Kubernetes finns i [Kubernetes-dokumentationen]( https://kubernetes.io/docs/home/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westeurope*.

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

Resultat:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster

Skapa ett Kubernetes-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#az-acs-create). I följande exempel skapas ett kluster med namnet *myK8sCluster* med en Linux-huvudnod och tre Linux-agentnoder.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

I vissa fall, som vid en begränsad utvärderingsversion, har en Azure-prenumeration begränsad åtkomst till Azure-resurser. Om distributionen misslyckas på grund av begränsade tillgängliga kärnor minskar du antalet standardagenter genom att lägga till `--agent-count 1` till kommandot [az acs create](/cli/azure/acs#az-acs-create). 

Efter en stund slutförs kommandot och returnerar json-formaterad information om klustret. 

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Hantera Kubernetes-kluster med [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes kommandoradsklient. 

Om du använder Azure CloudShell är kubectl redan installerat. Om du vill installera det lokalt, kan du använda kommandot [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Du konfigurerar kubectl att ansluta till ditt Kubernetes-kluster genom att köra kommandot [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). I det här steget laddar vi ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Resultat:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Köra programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I det här exemplet används ett manifest för att skapa alla objekt som behövs för att köra Azure Vote-programmet. 

Skapa en fil med namnet `azure-vote.yml` och kopiera följande YAML till den. Om du arbetar i Azure Cloud Shell, kan du skapa filen med vi eller Nano som i ett virtuellt eller fysiskt system.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Använd kommandot [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) för att köra programmet.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Resultat:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs skapas en [Kubernetes-tjänst](https://kubernetes.io/docs/concepts/services-networking/service/) som exponerar programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra. 

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) med argumentet `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Till en början visas **EXTERNAL-IP** för *azure-vote-front*-tjänsten som *pending* (väntande). När EXTERNAL-IP-adressen har ändrats från *pending* till en *IP-adress* använder du `CTRL-C` för att stoppa kubectl-övervakningsprocessen. 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Nu kan du bläddra till den externa IP-adressen för att se Azure Vote-appen.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Ta bort klustret
När klustret inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Hämta koden

I den här snabbstartsguiden har fördefinierade containeravbildningar använts för att skapa en Kubernetes-distribution. Den tillhörande programkoden, Dockerfile och Kubernetes-manifestfilen finns på GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden distribuerade du ett Kubernetes-kluster och distribuerade sedan ett flerbehållarprogram till det. 

Om du vill lära dig mer om Azure Container Service, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Hantera ett ACS Kubernets-kluster](./container-service-tutorial-kubernetes-prepare-app.md)
