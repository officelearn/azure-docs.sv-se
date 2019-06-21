---
title: Förhandsgranskning – skapa en Windows Server-behållare i ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster, distribuera ett program på en Windows Server-behållare i Azure Kubernetes Service (AKS) med hjälp av Azure CLI.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: twhitney
ms.openlocfilehash: a9887e923358b5658a365b5cfc88759eca2501e0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303560"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Förhandsgranskning – skapa en Windows Server-behållare i ett Azure Kubernetes Service (AKS)-kluster med Azure CLI

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här artikeln får distribuera du ett AKS-kluster med Azure CLI. Du kan också distribuera en ASP.NET-exempelprogrammet i en Windows Server-behållare till klustret.

Den här funktionen är för närvarande en förhandsversion.

![Bild som visar Bläddra till ASP.NET-exempelprogrammet](media/windows-container/asp-net-sample-app.png)

Den här artikeln förutsätter grundläggande kunskaper om vanliga Kubernetes-begrepp. Mer information finns i [Kubernetes viktiga begrepp för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.61 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="before-you-begin"></a>Innan du börjar

När du har skapat ditt kluster som kan köra Windows Server-behållare måste du lägga till en ny nod-pool. Att lägga till en ny nodpool täcks i ett senare steg, men du måste först aktivera några funktioner i förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning, delta i. De tillhandahålls för att samla in feedback och buggar från vår community. I förhandsversionen kan är inte dessa funktioner avsedda för användning i produktion. Funktioner i offentliga förhandsversioner omfattas ”bästa prestanda” support. Hjälp från teamen för AKS-teknisk support är tillgänglig under kontorstid Pacific tidszon (Stillahavstid) endast. Mer information finns i följande supportartiklar:
>
> * [AKS supportprinciper][aks-support-policies]
> * [Vanliga frågor om Azure-Support][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för aks-förhandsversion
    
CLI-kommandon för att skapa och hantera flera nodpooler är tillgängliga i den *aks-förhandsversion* CLI-tillägg. Installera den *förhandsversionen av aks* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till][az-extension-add] kommandot, som visas i följande exempel:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Om du tidigare har installerat den *förhandsversionen av aks* tillägg, installera alla tillgängliga uppdateringar med hjälp av den `az extension update --name aks-preview` kommando.

### <a name="register-windows-preview-feature"></a>Registrera Windows-funktionen för förhandsgranskning

Om du vill skapa ett AKS-kluster som kan använda flera nodpooler och kör Windows Server-behållare, dennes den *WindowsPreview* funktionen flaggor för din prenumeration. Den *WindowsPreview* funktionen använder också med flera noder poolen kluster och skalningsuppsättning för virtuella datorer att hantera distributionen och konfigurationen av Kubernetes-noderna. Registrera den *WindowsPreview* funktionen flaggan med hjälp av den [az funktionen registrera][az-feature-register] kommandot som visas i följande exempel:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Alla AKS-kluster som du skapar när du har registrerat den *WindowsPreview* funktionsflagga använda förhandsupplevelsen för klustret. Om du vill fortsätta att skapa kluster av regelbundna, fullt stöd inte aktivera förhandsversionsfunktioner för produktion-prenumerationer. Använd en separat test- eller Azure-prenumeration för att testa förhandsversionsfunktioner.

Det tar några minuter att slutföra registreringen. Kontrollera statusen registrering med den [az funktionslistan][az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

När registreringstillståndet är `Registered`, tryck på Ctrl + C för att stoppa övervakningen tillståndet.  Uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som har stöd för flera nodpooler:

* Det finns flera nodpooler för kluster som skapas när du har registrerat den *WindowsPreview*. Flera nodpooler är också tillgängliga om du registrerar den *MultiAgentpoolPreview* och *VMSSPreview* funktioner för din prenumeration. Du kan inte lägga till eller hantera nodpooler med ett AKS-kluster som skapats innan dessa funktioner som har registrerats.
* Du kan inte ta bort den första nod-adresspoolen.

Den här funktionen är i förhandsversion, begränsningar gäller följande ytterligare:

* AKS-klustret kan ha högst åtta nodpooler.
* AKS-klustret kan ha högst 400 noder poolerna åtta noden.
* Poolnamn för Windows Server-noden har en gräns på 6 tecken.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är där resource group metadata lagras, det är också där dina resurser kör i Azure om du inte anger en annan region under resursskapandet. Skapa en resurs med det [az gruppen skapa][az-group-create] kommando.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

> [!NOTE]
> Den här artikeln använder Bash syntax för kommandon i den här självstudien.
> Om du använder Azure Cloud Shell kan du se till att listrutan i det övre vänstra hörnet i Cloud Shell-fönstret har angetts till **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Följande exempelutdata visar den resursgrupp som skapats:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

För att köra ett AKS-kluster som har stöd för nodpooler för Windows Server-behållare, ditt kluster måste använda en princip för nätverk som använder [Azure CNI][azure-cni-about] (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]. Använd den [az aks skapa][az aks create] kommando för att skapa ett AKS-kluster med namnet *myAKSCluster*. Det här kommandot skapar de nödvändiga nätverksresurserna om de inte finns.
  * Klustret har konfigurerats med en nod
  * Den *windows adminlösenord* och *windows-administratörsanvändarnamn* parametrar ange administratörsautentiseringsuppgifter för alla Windows Server-behållare som skapats i klustret.

Ange ditt eget säkra *PASSWORD_WIN* (Kom ihåg att kommandona i den här artikeln har angetts i ett BASH-gränssnitt):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Om du får ett valideringsfel för lösenord kan du försöka med att skapa din resursgrupp i en annan region.
> Försök att klustret skapas med den nya resursgruppen.

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

## <a name="add-a-windows-server-node-pool"></a>Lägga till en pool för Windows Server-nod

Som standard skapas ett AKS-kluster med en nodpool som kan köra Linux-behållare. Använd `az aks nodepool add` att lägga till en ny nod-pool som kan köra Windows Server-behållare.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

Kommandot ovan skapar en ny nodpool med namnet *npwin* och lägger till den till den *myAKSCluster*. När du skapar en nodpool för att köra Windows Server-behållare är standardvärdet för *vm nodstorlek* är *Standard_D2s_v3*. Om du väljer att ange den *vm nodstorlek* parameter, kontrollera listan över [begränsade VM-storlekar][restricted-vm-sizes]. Den minsta rekommenderade storleken är *Standard_D2s_v3*. Ovanstående kommando använder också standard undernätet i det virtuella nätverket standard som skapas när du kör `az aks create`.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster måste du använda [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Installera `kubectl` lokalt, använda den [az aks install-cli][az-aks-install-cli] kommando:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är *Klar*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Köra programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här artikeln används ett manifest för att skapa alla objekt som behövs för att köra ASP.NET-exempelprogrammet i en Windows Server-behållare. Den här manifest innehåller en [Kubernetes-distribution][kubernetes-deployment] for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service] komma åt programmet från internet.

ASP.NET-exempelprogrammet tillhandahålls som en del av den [.NET Framework-exempel][dotnet-samples] och körs i en Windows Server-behållare. AKS kräver Windows Server-behållare ska väljas utifrån bilder av *Windows Server 2019* eller större. Kubernetes-manifestfil måste också definiera en [noden väljare][node-selector] som talar om AKS-klustret att köra din ASP.NET exempelprogrammet pod på en nod som kan köra Windows Server-behållare.

Skapa en fil med namnet `sample.yaml` och kopiera följande YAML-definition. Om du använder Azure Cloud Shell, kan du skapa filen med `vi` eller `nano` som om du arbetar i ett virtuellt eller fysiskt system:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Distribuera programmet med den [kubectl gäller][kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f sample.yaml
```

Följande Exempelutdata visar distributionen och tjänsten har skapats:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Inledningsvis den *extern IP-adress* för den *exempel* service visas som *väntande*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öppna en webbläsare på den externa IP-adressen för din tjänst om du vill se exempelappen i praktiken.

![Bild som visar Bläddra till ASP.NET-exempelprogrammet](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda den [az group delete][az-group-delete] att ta bort resursgruppen, behållartjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner för hur du tar bort tjänstens huvudnamn finns [AKS-tjänsten huvudnamn överväganden och borttagning av][sp-delete].

## <a name="next-steps"></a>Nästa steg

I den här artikeln har distribuerat ett Kubernetes-kluster och distribueras ett exempelprogram för ASP.NET i en Windows Server-behållare till den. [Komma åt Kubernetes-webbinstrumentpanel][kubernetes-dashboard] för klustret du nyss skapade.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudie om AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
