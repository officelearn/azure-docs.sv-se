---
title: Hantera Azure Kubernetes-kluster med webbgränssnitt
description: Lär dig hur du använder den inbyggda Kubernetes-instrumentpanelen för Webbgränssnitt med Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307933"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Komma åt Kubernetes-instrumentpanelen med Azure Kubernetes Service (AKS)

Kubernetes innehåller en webbinstrumentpanel som kan användas för grundläggande hanteringsåtgärder. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och hjälper dig att vissa grundläggande instrumentpanel-åtgärder. Mer information om Kubernetes-instrumentpanelen finns i [Kubernetes-instrumentpanel för Webbgränssnitt][kubernetes-dashboard].

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en `kubectl` anslutning med klustret. Om du vill skapa ett AKS-kluster finns i den [AKS-Snabbstart][aks-quickstart].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.27 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Starta Kubernetes-instrumentpanelen

Starta Kubernetes-instrumentpanelen med den [az aks Bläddra] [ az-aks-browse] kommando. I följande exempel öppnas instrumentpanelen för klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklingssystemet och Kubernetes-API och öppnar en webbläsare till Kubernetes-instrumentpanelen.

### <a name="for-rbac-enabled-clusters"></a>För RBAC-aktiverade kluster

Om AKS-klustret använder RBAC, en *ClusterRoleBinding* måste skapas innan du har korrekt åtkomst till instrumentpanelen. Som standard Kubernetes-instrumentpanelen har distribuerats med minimal läsbehörighet och visar RBAC åtkomstfel. Kubernetes-instrumentpanelen stöder för närvarande inte användaren tillhandahåller autentiseringsuppgifter för att avgöra vilken åtkomstnivå, i stället används de roller som beviljats till kontot. En Klusteradministratör kan välja att ge ytterligare åtkomst till den *kubernetes-instrumentpanelen* tjänstkonto, men det kan vara en vektor för eskalering. Du kan också integrera Azure Active Directory-autentisering för att ge en mer detaljerad nivå av åtkomst.

Du kan skapa en bindning med den [kubectl skapa clusterrolebinding] [ kubectl-create-clusterrolebinding] kommandot som visas i följande exempel. 

> [!WARNING]
> Det här exemplet bindningen gäller inte några ytterligare autentisering-komponenter och kan leda till osäkert användning. Kubernetes-instrumentpanelen är öppen för alla med åtkomst till URL: en. Exponera inte Kubernetes-instrumentpanelen offentligt.
>
> Mer information om hur du använder de olika autentiseringsmetoderna finns i wiki för Kubernetes-instrumentpanelen på [åtkomstkontroller][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Du kan nu komma åt Kubernetes-instrumentpanelen i klustret RBAC-aktiverade. Starta Kubernetes-instrumentpanelen med den [az aks Bläddra] [ az-aks-browse] kommandot som beskrivs i föregående steg.

## <a name="run-an-application"></a>Kör ett program

I Kubernetes-instrumentpanelen klickar du på den **skapa** knappen i det övre högra fönstret. Ge distributionen namnet `nginx` och ange `nginx:latest` för namnet på avbildningen. Under **Service**väljer **externa** och ange `80` för både porten och målport.

När du är klar klickar du på **distribuera** distributionen skapas.

![Dialogrutan Skapa i Kubernetes-tjänst](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Visa programmet

Statusinformation om programmet kan ses i Kubernetes-instrumentpanelen. När programmet körs kan har varje komponent en grön kryssrutan bredvid den.

![Kubernetes-Poddar](./media/container-service-kubernetes-ui/complete-deployment.png)

Om du vill se mer information om programmet poddarna, klickar du på **Poddar** i den vänstra menyn och välj sedan den **NGINX** pod. Här kan du se pod-specifik information, till exempel resursförbrukning.

![Kubernetes-resurser](./media/container-service-kubernetes-ui/running-pods.png)

För att hitta IP-adressen för programmet, klickar du på **Services** i den vänstra menyn och välj sedan den **NGINX** service.

![Visa nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och visa program, kan Kubernetes-instrumentpanelen användas för att redigera och uppdatera programdistributioner.

Om du vill redigera en distribution, klickar du på **distributioner** i den vänstra menyn och välj sedan den **NGINX** distribution. Välj slutligen **redigera** i det övre högra navigeringsfältet.

![Redigera Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Leta upp den `spec.replica` värde, som bör vara 1, ändra värdet till 3. Du gör det ökade replikantalet av NGINX-distribution från 1 till 3.

Välj **uppdatering** när redo.

![Redigera Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes-instrumentpanelen finns i Kubernetes-dokumentationen.

> [!div class="nextstepaction"]
> [Kubernetes-instrumentpanel för Webbgränssnitt][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
