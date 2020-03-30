---
title: Hantera ett Azure Kubernetes-tjänstkluster med webbinstrumentpanelen
description: Lär dig hur du använder den inbyggda kubernetes-webbgränssnittsinstrumentpanelen för att hantera ett AKS-kluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595356"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Få tillgång till Kubernetes webbinstrumentpanel i Azure Kubernetes Service (AKS)

Kubernetes innehåller en webbinstrumentpanel som kan användas för grundläggande hanteringsåtgärder. Med den här instrumentpanelen kan du visa grundläggande hälsostatus och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och guidar dig sedan genom några grundläggande instrumentpanelsåtgärder.

Mer information om kubernetes instrumentpanelen finns i [Kubernetes webbgränssnittsinstrumentpanel][kubernetes-dashboard].

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att `kubectl` du har skapat ett AKS-kluster och har upprättat en anslutning till klustret. Om du behöver skapa ett AKS-kluster läser du [snabbstarten för AKS][aks-quickstart].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Starta kubernetes-instrumentpanelen

Om du vill starta kubernetes-instrumentpanelen använder du kommandot [az aks browse.][az-aks-browse] I följande exempel öppnas instrumentpanelen för klustret *myAKSCluster* i resursgruppen *myResourceGroup:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklingssystemet och Kubernetes API och öppnar en webbläsare för kubernetes instrumentpanelen. Om en webbläsare inte är öppen för kubernetes-instrumentpanelen kopierar och klistrar du in url-adressen som anges i Azure CLI, vanligtvis `http://127.0.0.1:8001`.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Om AKS-klustret använder RBAC måste en *ClusterRoleBinding* skapas innan du kan komma åt instrumentpanelen på rätt sätt. Som standard distribueras Kubernetes-instrumentpanelen med minimal läsbehörighet och visar RBAC-åtkomstfel. Kubernetes-instrumentpanelen stöder för närvarande inte autentiseringsuppgifter för användartillhandahållna användare för att fastställa åtkomstnivån, utan använder de roller som beviljas tjänstkontot. En klusteradministratör kan välja att bevilja ytterligare åtkomst till *tjänstkontot för kubernetes-dashboard,* men det kan vara en vektor för eskalering av privilegier. Du kan också integrera Azure Active Directory-autentisering för att ge en mer detaljerad åtkomstnivå.
> 
> Om du vill skapa en bindning använder du kommandot [kubectl create clusterrolebinding.][kubectl-create-clusterrolebinding] I följande exempel visas hur du skapar en exempelbindning, men den här exempelbindningen tillämpar inga ytterligare autentiseringskomponenter och kan leda till osäker användning. Kubernetes-instrumentpanelen är öppen för alla som har åtkomst till webbadressen. Exponera inte Kubernetes instrumentpanelen offentligt.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Mer information om hur du använder de olika autentiseringsmetoderna finns i Kubernetes instrumentpanelswiki på [åtkomstkontroller][dashboard-authentication].

![Översiktssidan för Kubernetes webbinstrumentpanel](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Skapa ett program

Om du vill se hur kubernetes instrumentpanelen kan minska komplexiteten i hanteringsuppgifter, låt oss skapa ett program. Du kan skapa ett program från kubernetes instrumentpanelen genom att tillhandahålla textinmatning, en YAML-fil eller via en grafisk guide.

Så här skapar du ett program:

1. Välj knappen **Skapa** i det övre högra fönstret.
1. Om du vill använda den grafiska guiden väljer du att **skapa en app**.
1. Ange ett namn för distributionen, till exempel *nginx*
1. Ange namnet på den behållaravbildning som ska användas, till exempel *nginx:1.15.5*
1. Om du vill exponera port 80 för webbtrafik skapar du en Kubernetes-tjänst. Under **Tjänst**väljer du **Extern**och anger sedan **80** för både porten och målporten.
1. När du är klar väljer du **Distribuera** för att skapa appen.

![Distribuera en app i Kubernetes webbinstrumentpanel](./media/kubernetes-dashboard/create-app.png)

Det tar en minut eller två för en offentlig extern IP-adress som ska tilldelas kubernetes-tjänsten. Välj **Tjänster**under Identifiering **och belastningsutjämning** i den vänstra storleken . Programmets tjänst visas, inklusive de *externa slutpunkterna,* som visas i följande exempel:

![Visa lista över tjänster och slutpunkter](./media/kubernetes-dashboard/view-services.png)

Välj slutpunktsadressen om du vill öppna ett webbläsarfönster på standardsidan för NGINX:

![Visa standardsidan för NGINX för det distribuerade programmet](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Visa podsinformation

Kubernetes instrumentpanelen kan tillhandahålla grundläggande övervakningsmått och felsökningsinformation, till exempel loggar.

Om du vill se mer information om dina programpoddar väljer du **Poddar** i menyn till vänster. Listan över tillgängliga poddar visas. Välj *din nginx* pod för att visa information, till exempel resursförbrukning:

![Visa podsinformation](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och visa program kan kubernetes instrumentpanelen användas för att redigera och uppdatera programdistributioner. Om du vill ge ytterligare redundans för programmet ska vi öka antalet NGINX-repliker.

Så här redigerar du en distribution:

1. Välj **Distributioner** på menyn till vänster och välj sedan din *nginx-distribution.*
1. Välj **Redigera** i det övre högra navigeringsfältet.
1. Leta `spec.replica` reda på värdet, på runt rad 20. Om du vill öka antalet repliker för programmet ändrar du det här värdet från *1* till *3*.
1. Välj **Uppdatera** när du är klar.

![Redigera distributionen för att uppdatera antalet repliker](./media/kubernetes-dashboard/edit-deployment.png)

Det tar en stund för de nya poddar som ska skapas inuti en replikuppsättning. På menyn till vänster väljer du **Replikuppsättningar**och väljer sedan din *nginx-replikuppsättning.* Listan över poddar återspeglar nu det uppdaterade replikantalet, vilket visas i följande exempelutdata:

![Visa information om replikuppsättningen](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Nästa steg

Mer information om kubernetes-instrumentpanelen finns i [Kubernetes webbgränssnittsinstrumentpanel][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
