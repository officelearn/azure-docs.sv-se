---
title: Hantera ett Azure Kubernetes service-kluster med webb instrument panelen
description: Lär dig hur du använder det inbyggda Kubernetes-webbgränssnittets instrument panel för att hantera ett Azure Kubernetes service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77595356"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Få åtkomst till Kubernetes-webbinstrumentpanelen i Azure Kubernetes service (AKS)

Kubernetes innehåller en webb instrument panel som kan användas för grundläggande hanterings åtgärder. På den här instrument panelen kan du visa grundläggande hälso status och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och vägleder dig genom några grundläggande instrument panels åtgärder.

Mer information om instrument panelen för Kubernetes finns i [webb gränssnitts instrument panelen för Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster `kubectl` och har upprättat en anslutning till klustret. Om du behöver skapa ett AKS-kluster kan du läsa [snabb][aks-quickstart]starten för AKS.

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Starta Kubernetes-instrumentpanelen

Starta Kubernetes-instrumentpanelen med kommandot [AZ AKS Browse][az-aks-browse] . I följande exempel öppnas instrument panelen för klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklings systemet och Kubernetes-API: et och öppnar en webbläsare till Kubernetes-instrumentpanelen. Om en webbläsare inte öppnas på Kubernetes-instrumentpanelen kopierar du och klistrar in URL-adressen som anges i Azure CLI `http://127.0.0.1:8001`, vanligt vis.

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
> Om ditt AKS-kluster använder RBAC måste du skapa en *ClusterRoleBinding* innan du kan få åtkomst till instrument panelen korrekt. Som standard distribueras instrument panelen Kubernetes med minimal Läs behörighet och visar RBAC-åtkomst fel. Kubernetes-instrumentpanelen stöder för närvarande inte användarens autentiseringsuppgifter för att fastställa åtkomst nivån, i stället använder den roller som har tilldelats tjänst kontot. En kluster administratör kan välja att bevilja ytterligare åtkomst till *Kubernetes-instrument panelens* tjänst konto, men det kan vara en Vector för eskalering av privilegier. Du kan också integrera Azure Active Directory-autentisering för att ge en mer detaljerad åtkomst nivå.
> 
> Om du vill skapa en bindning använder du kommandot [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] . I följande exempel visas hur du skapar en exempel bindning, men den här exempel bindningen tillämpar inte några ytterligare autentiserings komponenter och kan leda till osäker användning. Kubernetes-instrumentpanelen är öppen för alla som har åtkomst till URL: en. Exponera inte Kubernetes-instrumentpanelen offentligt.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Mer information om hur du använder olika autentiseringsmetoder finns i Kubernetes-instrumentpanelen wiki på [åtkomst kontroller][dashboard-authentication].

![Sidan översikt på Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Skapa ett program

För att se hur Kubernetes-instrumentpanelen kan minska komplexiteten med hanterings uppgifter ska vi skapa ett program. Du kan skapa ett program från Kubernetes-instrumentpanelen genom att tillhandahålla text indata, en YAML-fil eller en grafisk guide.

Utför följande steg för att skapa ett program:

1. Välj knappen **skapa** i det övre högra fönstret.
1. Om du vill använda den grafiska guiden väljer du att **skapa en app**.
1. Ange ett namn för distributionen, till exempel *nginx*
1. Ange namnet på behållar avbildningen som ska användas, t. ex. *nginx: 1.15.5*
1. För att exponera port 80 för webb trafik skapar du en Kubernetes-tjänst. Under **tjänst**väljer du **extern**och anger sedan **80** för både port-och mål porten.
1. När du är klar väljer du **distribuera** för att skapa appen.

![Distribuera en app i Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/create-app.png)

Det tar en minut eller två för en offentlig extern IP-adress som ska tilldelas till Kubernetes-tjänsten. På den vänstra storleken under **identifiering och belastnings utjämning** väljer du **tjänster**. Programmets tjänst visas, inklusive *externa slut punkter*, som du ser i följande exempel:

![Visa lista över tjänster och slut punkter](./media/kubernetes-dashboard/view-services.png)

Välj slut punkts adress för att öppna ett webbläsarfönster till standard NGINX-sidan:

![Visa standard NGINX-sidan för det distribuerade programmet](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Visa information om Pod

Kubernetes-instrumentpanelen kan ge grundläggande övervaknings mått och felsöknings information, till exempel loggar.

Om du vill se mer information om din program poddar väljer du **poddar** i den vänstra menyn. Listan över tillgängliga poddar visas. Välj din *nginx* -Pod för att visa information, till exempel resursförbrukning:

![Visa information om Pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Redigera programmet

Förutom att skapa och Visa program kan Kubernetes-instrumentpanelen användas för att redigera och uppdatera program distributioner. För att ge ytterligare redundans för programmet ska vi öka antalet NGINX-repliker.

Redigera en distribution:

1. Välj **distributioner** på menyn till vänster och välj sedan din *nginx* -distribution.
1. Välj **Redigera** i det övre högra navigerings fältet.
1. Leta upp `spec.replica` värdet på rad 20. Om du vill öka antalet repliker för programmet ändrar du värdet från *1* till *3*.
1. Välj **Uppdatera** när du är klar.

![Redigera distributionen för att uppdatera antalet repliker](./media/kubernetes-dashboard/edit-deployment.png)

Det tar en stund för den nya poddar att skapas i en replik uppsättning. Välj **replik uppsättningar**på den vänstra menyn och välj sedan *nginx* replik uppsättning. Listan över poddar visar nu det uppdaterade antalet repliker, vilket visas i följande exempel på utdata:

![Visa information om replik uppsättningen](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Nästa steg

Mer information om instrument panelen för Kubernetes finns på [instrument panelen för Kubernetes-WEBBgränssnittet][kubernetes-dashboard].

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
