---
title: Hantera ett Azure Kubernetes service-kluster med webb instrument panelen
description: Lär dig hur du använder det inbyggda Kubernetes-webbgränssnittets instrument panel för att hantera ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126858"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Få åtkomst till Kubernetes-webbinstrumentpanelen i Azure Kubernetes service (AKS)

Kubernetes innehåller en webb instrument panel som kan användas för grundläggande hanterings åtgärder. På den här instrument panelen kan du visa grundläggande hälso status och mått för dina program, skapa och distribuera tjänster och redigera befintliga program. Den här artikeln visar hur du kommer åt Kubernetes-instrumentpanelen med hjälp av Azure CLI och vägleder dig genom några grundläggande instrument panels åtgärder.

Mer information om instrument panelen för Kubernetes finns i [webb gränssnitts instrument panelen för Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster `kubectl` och har upprättat en anslutning till klustret. Om du behöver skapa ett AKS-kluster kan du läsa [snabb][aks-quickstart]starten för AKS.

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Starta Kubernetes-instrumentpanelen

Starta Kubernetes-instrumentpanelen med kommandot [AZ AKS Browse][az-aks-browse] . I följande exempel öppnas instrument panelen för klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Det här kommandot skapar en proxy mellan utvecklings systemet och Kubernetes-API: et och öppnar en webbläsare till Kubernetes-instrumentpanelen. Om en webbläsare inte öppnas på Kubernetes-instrumentpanelen kopierar du och klistrar in URL-adressen som anges i Azure CLI `http://127.0.0.1:8001`, vanligt vis.

![Inloggnings sidan för Kubernetes-webbinstrumentpanelen](./media/kubernetes-dashboard/dashboard-login.png)

Du har följande alternativ för att logga in på ditt klusters instrument panel:

* En [kubeconfig-fil][kubeconfig-file]. Du kan skapa en kubeconfig-fil med [AZ AKS get-credentials][az-aks-get-credentials].
* En token, till exempel en token för ett [tjänst konto][aks-service-accounts] eller en användartoken. I [AAD-aktiverade kluster][aad-cluster]skulle denna token vara en AAD-token. Du kan använda `kubectl config view` för att visa en lista över tokens i din kubeconfig-fil. Mer information om hur du skapar en AAD-token för användning med ett AKS-kluster finns i [integrera Azure Active Directory med Azure Kubernetes service med Azure CLI][aad-cluster].
* Standard kontot för instrument panels tjänsten som används om du klickar på *hoppa över*.

> [!WARNING]
> Exponera aldrig Kubernetes-instrumentpanelen offentligt, oavsett vilken autentiseringsmetod som används.
> 
> När du konfigurerar autentisering för Kubernetes-instrumentpanelen rekommenderar vi att du använder en token via standard kontot för instrument panelen. Med en token kan varje användare använda sina egna behörigheter. Med standard kontot för instrument panels tjänsten kan en användare kringgå sina egna behörigheter och använda tjänst kontot i stället.
> 
> Om du väljer att använda standard kontot för instrument panels tjänsten och ditt AKS-kluster använder RBAC, måste du skapa en *ClusterRoleBinding* innan du kan få åtkomst till instrument panelen korrekt. Som standard distribueras instrument panelen Kubernetes med minimal Läs behörighet och visar RBAC-åtkomst fel. En kluster administratör kan välja att bevilja ytterligare åtkomst till *Kubernetes-instrument panelens* tjänst konto, men det kan vara en Vector för eskalering av privilegier. Du kan också integrera Azure Active Directory-autentisering för att ge en mer detaljerad åtkomst nivå.
>
> Om du vill skapa en bindning använder du kommandot [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] som visas i följande exempel. **Den här exempel bindningen tillämpar inte några ytterligare autentiseringspaket och kan leda till osäker användning.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Nu kan du komma åt Kubernetes-instrumentpanelen i ditt RBAC-aktiverade kluster. Starta Kubernetes-instrumentpanelen med hjälp av kommandot [AZ AKS Browse][az-aks-browse] enligt beskrivningen i föregående steg.
>
> Om klustret inte använder RBAC, rekommenderar vi inte att skapa en *ClusterRoleBinding*.
> 
> Mer information om hur du använder olika autentiseringsmetoder finns i Kubernetes-instrumentpanelen wiki på [åtkomst kontroller][dashboard-authentication].

När du har valt en metod för att logga in visas instrument panelen för Kubernetes. Om du väljer att använda *token* eller *Skip*, använder Kubernetes-instrumentpanelen behörigheter för den inloggade användaren för att komma åt klustret.

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

Mer information om instrument panelen för Kubernetes finns på [instrument panelen för Kubernetes-][kubernetes-dashboard]webbgränssnittet.

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
