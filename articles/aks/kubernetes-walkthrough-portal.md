---
title: Skapa ett AKS-kluster i portalen
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du snabbt kan skapa ett Kubernetes-kluster, distribuera ett program och övervaka prestanda i Azure Kubernetes Service (AKS) med hjälp av Azure Portal.
services: container-service
ms.topic: quickstart
ms.date: 01/21/2020
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: e91e5e2701b02ab4c705cd80117dfc7689c7d054
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668442"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Snabbstart: Distribuera ett AKS-kluster (Azure Kubernetes Service) med Azure-portalen

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabbstarten ska du distribuera ett AKS-kluster med hjälp av Azure-portalen. Ett flerbehållarprogram som består av en webbklientdel och en Redis-instans körs sedan i klustret. Då ser du hur du övervakar hälsotillståndet för klustret och poddar som kör programmet.

![Bild som illustrerar hur du navigerar till Azure Vote-exempelprogram](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Du skapar ett AKS-kluster genom att slutföra följande steg:

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.

2. Välj **Behållare** >  **Kubernetes Service**.

3. Konfigurera följande alternativ på sidan **Grunderna:**
    - **Projektinformation:** Välj en **Azure-prenumeration**och välj eller skapa sedan en Azure **Resource-grupp,** till exempel *myResourceGroup*.
    - **Klusterinformation**: Ange ett **Kubernetes-klusternamn,** till exempel *myAKSCluster*. Välj en **region-,** **Kubernetes-version**och **DNS-namnprefix** för AKS-klustret.
    - **Primär nodpool:** Välj en **VM-nodstorlek** för AKS-noderna. Vm-storleken *kan inte* ändras när ett AKS-kluster har distribuerats. 
            - Välj antalet noder som ska distribueras till klustret. För den här snabbstarten ställer du in **Nodantal** till *1*. Antalet noder *kan* justeras efter att klustret har distribuerats.
    
    ![Skapa AKS-kluster – ange grundläggande information](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Välj **Nästa: Skala** när du är klar.

4. Behåll standardalternativen på sidan **Skala.** Klicka på **Nästa: Autentisering**längst ned på skärmen .
    > [!CAUTION]
    > Det kan ta flera minuter att skapa nya AAD-tjänsthuvudnamn för att sprida och bli tillgängligt, vilket gör att tjänsthuvudtillstånd inte hittades fel och valideringsfel i Azure-portalen. Om du träffar detta besök [här](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one) för mildrande åtgärder.

5. Konfigurera följande alternativ på sidan **Autentisering:**
    - Skapa ett nytt tjänsthuvudnamn genom att lämna fältet **Tjänsthuvudnamn** med **(nytt) standardtjänsthuvudnamn**. Eller så kan du välja *Konfigurera tjänstens huvudnamn* för att använda ett befintligt. Om du använder ett befintligt måste du ange SPN-klient-ID och hemligt.
    - Aktivera alternativet för kontroller för rollbaserad åtkomstkontroll (RBAC) för Kubernetes. Detta ger mer detaljerad kontroll över åtkomsten till Kubernetes-resurserna som distribueras i AKS-klustret.

Som standard används *Grundläggande* nätverk och Azure Monitor för container är aktiverat. Klicka på **Granska + skapa** och sedan **skapa** när valideringen är klar.

Det tar några minuter att skapa AKS-klustret. När distributionen är klar klickar du på **Gå till resurs**eller bläddrar till AKS-klusterresursgruppen, till exempel *myResourceGroup,* och väljer AKS-resursen, till exempel *myAKSCluster*. AKS-klusterinstrumentpanelen visas, som i det här exemplet:

![Exempel på AKS-instrumentpanel i Azure-portalen](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. `kubectl`-klienten är förinstallerad i Azure Cloud Shell.

Öppna Cloud Shell `>_` med knappen högst upp i Azure-portalen.

![Öppna Azure Cloud Shell i portalen](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem. I följande exempel hämtas autentiseringsuppgifterna för klusternamnet *myAKSCluster* i den resursgrupp som heter *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är *Klar*:

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Köra appen

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här snabbstarten används ett manifest för att skapa alla objekt som behövs för att köra Azure Vote-programmet. Det här manifestet innehåller två [Kubernetes-distributioner][kubernetes-deployment] – en för exemplet på Azure Vote Python-program och den andra för en Redis-instans. Två [Kubernetes-tjänster][kubernetes-service] skapas också – en intern tjänst för Redis-instansen och en extern tjänst för att komma åt Azure Vote-programmet från Internet.

> [!TIP]
> I den här snabbstarten skapar och distribuerar du manuellt applikationsmanifest till AKS-klustret. I verkliga scenarier kan du använda [Azure Dev Spaces][azure-dev-spaces] för att snabbt iterera och felsöka koden direkt i AKS-klustret. Du kan använda Dev Spaces på olika OS-plattformar och i olika utvecklingsmiljöer samt arbeta tillsammans med andra i ditt team.

I Cloud Shell använder `nano azure-vote.yaml` du `vi azure-vote.yaml` antingen kommandot eller `azure-vote.yaml`för att skapa en fil med namnet . Kopiera sedan i följande YAML-definition:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
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
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
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

Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f azure-vote.yaml
```

Följande exempelutdata visar de distributioner och tjänster som skapats:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Till en början visas *EXTERNAL-IP* för *azure-vote-front*-tjänsten som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som illustrerar hur du navigerar till Azure Vote-exempelprogram](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Övervaka hälsotillstånd och loggar

När du skapade klustret aktiverades Azure Monitor för container. Den här övervakningsfunktionen tillhandahåller hälsomått för både AKS-klustret och de poddar som körs i klustret.

Det kan ta några minuter för dessa data att hämtas till Azure Portal. Om du vill se aktuell status, drifttid och resursanvändning för Azure Vote poddarna bläddrar du tillbaka till AKS-resursen i Azure-portalen, till exempel *myAKSCluster*. Du kan sedan komma åt hälsostatusen så här:

1. Under **Övervakning** väljer du **Insights ** på vänster sida
1. Överst väljer du **+ Lägg till filter**
1. Välj *Namnrymd* som egenskapen, och välj sedan *\<Alla förutom kube-system\>*
1. Välja att visa **containrarna**.

Containrarna *bak-azure-vote* och *azure-vote-front* visas enligt följande exempel:

![Visa hälsan för containrar som körs i AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Om du vill `azure-vote-front` visa loggar för podden väljer du **listrutan Visa behållarloggar** i listrutan i behållarelistan. Dessa loggar inkluderar *stdout-* och *stderr-strömmar* från behållaren.

![Visa containerloggarna i AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Ta bort klustret

När klustret inte längre behövs, kan du ta bort klusterresursen. Alla associerade resurser tas då också bort. Den här åtgärden kan slutföras i Azure-portalen genom att välja knappen **Ta bort** på AKS-klusterinstrumentpanelen. Alternativt kan kommandot [az aks delete][az-aks-delete] användas i Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].

## <a name="get-the-code"></a>Hämta koden

I den här snabbstarten har fördefinierade containeravbildningar användes för att skapa en Kubernetes-distribution. Den tillhörande programkoden, Dockerfile och Kubernetes-manifestfilen finns på GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden distribuerade du ett Kubernetes-kluster och distribuerade sedan ett flercontainerprogram till det.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
