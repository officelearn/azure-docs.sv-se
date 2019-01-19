---
title: (INAKTUELL) Snabbstart – Azure Kubernetes-kluster för Windows
description: Lär dig snabbt att skapa ett Kubernetes-kluster på Windows-behållare i Azure Container Service med Azure CLI.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 90608e95cee6b5b7231419c2c65c39741f106b0f
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412651"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-windows-containers"></a>(INAKTUELL) Distribuera Kubernetes-kluster för Windows-behållare

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur Azure CLI används för att distribuera ett [Kubernetes](https://kubernetes.io/docs/home/)-kluster i [Azure Container Service](../container-service-intro.md). När klustret distribueras kan du ansluta till det med kommandoradsverktyget för Kubernetes `kubectl` och distribuera din första Windows-container.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Stöd för Windows-behållare med Kubernetes i Azure Container Service är tillgängligt som en förhandsversion. 
>

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster
Skapa ett Kubernetes-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#az-acs-create). 

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

Om du använder Azure CloudShell är `kubectl` redan installerad. Om du vill installera det lokalt, kan du använda kommandot [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

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

## <a name="deploy-a-windows-iis-container"></a>Distribuera en Windows-IIS-container

Du kan köra en Docker-container i en Kubernetes-*pod*, som innehåller en eller flera containrar. 

Det här grundläggande exemplet använder en JSON-fil för att ange en container för Microsoft Internet Information Server (IIS) och skapar sedan poden med hjälp av `kubctl apply`. 

Skapa en lokal fil med namnet `iis.json` och kopiera följande text. Filen uppmanar Kubernetes att köra IIS på Windows Server 2016 Nano Server med en offentlig containeravbildning från [Docker Hub](https://hub.docker.com/r/microsoft/iis/). Containern använder port 80, men är ursprungligen endast tillgänglig i nätverket för klustret.

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

Medan poden distribueras är statusen `ContainerCreating`. Det kan ta några minuter för containern att ange tillståndet `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Om du vill exponera din pod för alla med en offentlig IP-adress skriver du följande kommando:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Med detta kommando skapar Kubernetes en tjänst och en Azure load balancer-regel med en offentlig IP-adress för tjänsten. 

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
När klustret inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden du har distribuerat ett Kubernetes-kluster med `kubectl` och distribuerat en pod med en IIS-container. Om du vill veta mer om Azure Container Service kan fortsätta till självstudiekursen om Kubernetes.

> [!div class="nextstepaction"]
> [Hantera ett ACS Kubernets-kluster](container-service-tutorial-kubernetes-prepare-app.md)
