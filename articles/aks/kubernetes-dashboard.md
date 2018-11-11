---
title: Hantera ett Azure Kubernetes Service-kluster med web-instrumentpanelen
description: Lär dig hur du använder den inbyggda Kubernetes-instrumentpanelen för Webbgränssnitt för att hantera ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 9d953cdb82412c07fe0ed4bef75dece4a929cad9
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299137"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Komma åt Kubernetes web-instrumentpanelen i Azure Kubernetes Service (AKS)

Kubernetes innehåller en webbinstrumentpanel som kan användas för grundläggande hanteringsåtgärder. Den här instrumentpanelen kan du visa grundläggande hälsostatus och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och hjälper dig att vissa grundläggande instrumentpanel-åtgärder.

Mer information om Kubernetes-instrumentpanelen finns i [Kubernetes-instrumentpanel för Webbgränssnitt][kubernetes-dashboard].

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en `kubectl` anslutning med klustret. Om du vill skapa ett AKS-kluster finns i den [AKS-Snabbstart][aks-quickstart].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Starta Kubernetes-instrumentpanelen

Starta Kubernetes-instrumentpanelen med den [az aks Bläddra] [ az-aks-browse] kommando. I följande exempel öppnas instrumentpanelen för klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklingssystemet och Kubernetes-API och öppnar en webbläsare till Kubernetes-instrumentpanelen. Om en webbläsare inte öppnas Kubernetes-instrumentpanelen, kopiera och klistra in URL-adressen som anges i Azure CLI, vanligtvis *http://127.0.0.1:8001*.

![Översiktssidan för Kubernetes web-instrumentpanelen](./media/kubernetes-dashboard/dashboard-overview.png)

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

## <a name="create-an-application"></a>Skapa ett program

Om du vill se hur Kubernetes-instrumentpanelen kan minska komplexiteten av hanteringsuppgifter, nu ska vi skapa ett program. Du kan skapa ett program på Kubernetes-instrumentpanelen genom att tillhandahålla textinmatning, en YAML-fil eller via en grafisk Guide.

Om du vill skapa ett program utför du följande steg:

1. Välj den **skapa** knappen i det övre högra fönstret.
1. Om du vill använda grafisk guide, välja att **skapa en app**.
1. Ange ett namn för distributionen, till exempel *nginx*
1. Ange namnet för behållaravbildningen att använda, till exempel *nginx:1.15.5*
1. Om du vill exponera port 80 för webbtrafik, skapar du ett Kubernetes-tjänst. Under **Service**väljer **externa**, ange sedan **80** för både porten och målport.
1. När du är klar väljer **distribuera** att skapa appen.

![Distribuera en app i Kubernetes web-instrumentpanelen](./media/kubernetes-dashboard/create-app.png)

Det tar en minut eller två för en offentlig extern IP-adress som ska tilldelas till Kubernetes-tjänst. På den vänstra storleken under **identifiering och belastningsutjämning** Välj **Services**. Tjänsten för ditt program visas, inklusive den *externa slutpunkter*, enligt följande exempel:

![Visa en lista över tjänster och slutpunkter](./media/kubernetes-dashboard/view-services.png)

Välj slutpunktsadressen att öppna ett webbläsarfönster till NGINX-standardsida:

![Visa NGINX standardsidan för det distribuerade programmet](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Visa pod information

Kubernetes-instrumentpanelen kan ge grundläggande övervakning av mått- och felsökningsinformation, till exempel loggar.

Om du vill se mer information om programmet poddarna **Poddar** på den vänstra menyn. Listan över tillgängliga poddar visas. Välj din *nginx* pod att visa information, till exempel användning av databasresurser:

![Visa pod information](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och visa program, kan Kubernetes-instrumentpanelen användas för att redigera och uppdatera programdistributioner. För ytterligare redundans för programmet kan vi öka antalet repliker för NGINX.

Så här redigerar du en distribution:

1. Välj **distributioner** på den vänstra menyn och välj sedan din *nginx* distribution.
1. Välj **redigera** i det övre högra navigeringsfältet.
1. Leta upp den `spec.replica` värde, på runt rad 20. För att öka antalet repliker för programmet kan ändra det här värdet från *1* till *3*.
1. Välj **uppdatering** när redo.

![Redigera distributionen för att uppdatera antalet repliker](./media/kubernetes-dashboard/edit-deployment.png)

Det tar en liten stund för nya poddarna som ska skapas i en replikuppsättning. I den vänstra menyn, Välj **replikuppsättningar**, och välj sedan din *nginx* replikuppsättningen. Listan över poddar nu visar uppdaterade replikantalet, som visas i följande Exempelutdata:

![Visa information om uppsättningen](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes-instrumentpanelen finns i den [Kubernetes-instrumentpanel för Webbgränssnitt][kubernetes-dashboard].

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
