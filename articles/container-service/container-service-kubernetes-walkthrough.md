---
title: "Snabbstart - Azure Kubernetes-kluster för Linux | Microsoft Docs"
description: "Lär dig snabbt att skapa ett Kubernetes-kluster på Linux-behållare i Azure Container Service med Azure CLI."
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: sv-se
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Distribuera Kubernets-kluster för Linux-behållare

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur Azure CLI används för att distribuera ett [Kubernetes](https://kubernetes.io/docs/home/)-kluster i [Azure Container Service](container-service-intro.md). När klustret distribueras kan du ansluta till det med kommandoradsverktyget för Kubernetes `kubectl` och distribuera din första Linux-behållare.

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Skapa Kubernetes-kluster
Skapa ett Kubernetes-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#create). 

I följande exempel skapas ett kluster med namnet *myK8sCluster* med en Linux master-nod och två noder för Linux-agenten. Det här exemplet skapar SSH-nycklar, om de inte redan finns i standardkatalogen. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Uppdatera dessa klusternamn till något som är lämpligt för din miljö. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Om några minuter har kommandot slutförts och visar information om din distribution.

## <a name="install-kubectl"></a>Installera kubectl

För att ansluta till Kubernetes-klustret från klientdatorn använder du [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes kommandoradsklient. 

Om du använder Azure CloudShell är `kubectl` redan installerad. Om du vill installera det lokalt, kan du använda kommandot [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

I följande exempel för Azure CLI installeras `kubectl` i systemet. Om du använder Azure CLI på macOS- eller Linux behöva du köra kommandot med `sudo`.

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


## <a name="deploy-an-nginx-container"></a>Distribuera en NGINX-behållare

Du kan köra en dockerbehållare i en Kubernetes-*pod*, som innehåller en eller flera behållare. 

Det här kommandot startar Docker-behållaren NGINX i en Kubernetes-pod på en av noderna. I det här fallet kör behållaren NGINX-webbservern från en avbildning i [Docker-hubb](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
För att se att behållaren körs, kör du:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan
Om du vill exponera NGINX-servern för alla med en offentlig IP-adress skriver du följande kommando:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Med detta kommando skapar Kubernetes en tjänst och en [Azure Load Balancer-regel](container-service-kubernetes-load-balancing.md) med en offentlig IP-adress för tjänsten. 

Kör följande kommando för att se status för tjänsten.

```azurecli-interactive
kubectl get svc
```

IP-adressen visas först som `pending`. Efter några minuter har tjänstens externa IP-adress ställts in:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Du kan använda en webbläsare som du väljer för att se NGINX-välkomstsidan på den externa IP-adressen:

![Bild som visar hur du går till Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Ta bort klustret
När klustret inte längre behövs du använda kommandot [az group delete](/cli/azure/group#delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguide du har distribuerat ett Kubernetes-kluster med `kubectl`, och distribuerat en pod med en NGINX-behållare. Om du vill veta mer om Azure Container Service kan fortsätta till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Hantera ett ACS Kubernets-kluster](./container-service-tutorial-kubernetes-prepare-app.md)

