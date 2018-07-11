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
ms.openlocfilehash: e197a251df3f34e5416bafacfd54a3fc7f51d503
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928224"
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

Om AKS-klustret använder RBAC, en *ClusterRoleBinding* måste skapas innan du kan komma åt instrumentpanelen. Utan en roll-bindning, kan Azure CLI returnerar ett fel liknar följande exempel:

```
error: unable to forward port because pod is not running. Current status=Pending
```

Skapa en bindning kan skapa en fil med namnet *instrumentpanelen admin.yaml* och klistra in följande exempel. Det här exemplet bindningen gäller inte några ytterligare autentisering-komponenter. Du kan använda metoder, till exempel ägar-token eller ett användarnamn/lösenord för att styra vem som kan komma åt instrumentpanelen och vilka behörigheter de har. Mer information om autentiseringsmetoder finns i wiki för Kubernetes-instrumentpanelen på [åtkomstkontroller][dashboard-authentication].

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

Tillämpa bindning med [kubectl gäller] [ kubectl-apply] och ange din *instrumentpanelen admin.yaml*, enligt följande exempel:

```
$ kubectl apply -f dashboard-admin.yaml

clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
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
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
