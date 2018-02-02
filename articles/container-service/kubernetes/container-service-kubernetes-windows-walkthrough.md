---
title: "Snabbstart - Azure Kubernetes kluster för Windows"
description: "Lär dig snabbt att skapa ett Kubernetes-kluster på Windows-behållare i Azure Container Service med Azure CLI."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: a7bb330657d14ac42cddf2e20fbb2e5a5b2a589b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>Distribuera Kubernets-kluster för Windows-behållare

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur Azure CLI används för att distribuera ett [Kubernetes](https://kubernetes.io/docs/home/)-kluster i [Azure Container Service](../container-service-intro.md). När klustret distribueras kan du ansluta till det med kommandoradsverktyget för Kubernetes `kubectl` och distribuera din första Windows-behållare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Stöd för Windows-behållare med Kubernetes i Azure Container Service är tillgängligt som en förhandsversion. 
>

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster
Skapa ett Kubernetes-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#az_acs_create). 

I följande exempel skapas ett kluster med namnet *myK8sCluster* med en Linux master-nod och två noder för Windows-agenten. Det här exemplet skapar SSH-nycklar som krävs för att ansluta till Linux-mastern. Det här exemplet använder *azureuser* som administrativt användarnamn och *myPassword12* som lösenord. Uppdatera dessa värden till något som är lämpligt för din miljö. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Om några minuter har kommandot slutförts och visar information om din distribution.

## <a name="install-kubectl"></a>Installera kubectl

För att ansluta till Kubernetes-klustret från klientdatorn använder du [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes kommandoradsklient. 

Om du använder Azure CloudShell är `kubectl` redan installerad. Om du vill installera det lokalt, kan du använda kommandot [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

I följande exempel för Azure CLI installeras `kubectl` i systemet. I Windows kör du kommandot som administratör.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Ansluta med kubectl

För att konfigurera `kubectl` för att ansluta till Kubernetes klustret kör du kommandot [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). I följande exempel hämtas klusterkonfigurationen för Kubernetes-klustret.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

För att verifiera anslutningen till ditt kluster från din dator, kör:

```azurecli-interactive
kubectl get nodes
```

`kubectl` visar master- och agentnoderna.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Distribuera en Windows-IIS-behållare

Du kan köra en dockerbehållare i en Kubernetes-*pod*, som innehåller en eller flera behållare. 

Det här grundläggande exemplet använder en JSON-fil för att ange en behållare för Microsoft Internet Information Server (IIS) och skapar sedan poden med hjälp av `kubctl apply`. 

Skapa en lokal fil med namnet `iis.json` och kopiera följande text. Filen uppmanar Kubernetes att köra IIS på Windows Server 2016 Nano Server med en offentlig avbildning från [Docker Hub](https://hub.docker.com/r/microsoft/iis/). Behållaren använder port 80, men är ursprungligen endast tillgänglig i nätverket för klustret.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Om du vill starta en pod, skriver du:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

För att spåra distributionen, skriver du:
  
```azurecli-interactive
kubectl get pods
```

Medan poden distribueras är statusen `ContainerCreating`. Det kan ta några minuter för poden att ange tillståndet `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Om du vill exponera din pod för alla med en offentlig IP-adress skriver du följande kommando:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Med det här kommandot skapar en tjänst och en Azure-regel för belastningsutjämning med en offentlig IP-adress för tjänsten Kubernetes. 

Kör följande kommando för att se status för tjänsten.

```azurecli-interactive
kubectl get svc
```

IP-adressen visas först som `pending`. Efter några minuter har `iis`-podens externa IP-adress ställts in:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Du kan använda en webbläsare som du väljer för att se IIS-välkomstsidan på den externa IP-adressen:

![Bild som visar hur du går till IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Ta bort klustret
När klustret inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguide du har distribuerat ett Kubernetes-kluster med `kubectl` och distribuerat en pod med en IIS-behållare. Om du vill veta mer om Azure Container Service kan fortsätta till självstudiekursen om Kubernetes.

> [!div class="nextstepaction"]
> [Hantera ett ACS Kubernets-kluster](container-service-tutorial-kubernetes-prepare-app.md)
