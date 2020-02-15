---
title: För hands version – skapa en Windows Server-behållare i ett Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster, distribuerar ett program i en Windows Server-behållare i Azure Kubernetes service (AKS) med hjälp av Azure CLI.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/27/2020
ms.author: mlearned
ms.openlocfilehash: 0583e773a344a6786d13a5da30be24369d75f11f
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251710"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>För hands version – skapa en Windows Server-behållare i ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här artikeln distribuerar du ett AKS-kluster med hjälp av Azure CLI. Du distribuerar också ett ASP.NET-exempel program i en Windows Server-behållare till klustret.

Den här funktionen är för närvarande en förhandsversion.

![Bild av bläddring till ASP.NET exempel program](media/windows-container/asp-net-sample-app.png)

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes-koncept. Mer information finns i [Kubernetes Core Concepts for Azure Kubernetes service (AKS)][kubernetes-concepts].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.0.61 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="before-you-begin"></a>Innan du börjar

Du måste lägga till ytterligare en Node-pool när du har skapat klustret som kan köra Windows Server-behållare. Att lägga till ytterligare en Node-pool beskrivs i ett senare steg, men du måste först aktivera några få för hands versions funktioner.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill använda Windows Server-behållare behöver du *AKS-Preview CLI-* tillägg version 0.4.12 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök sedan efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registrera Windows för hands versions funktion

Om du vill skapa ett AKS-kluster som kan använda flera nodkonfigurationer och köra Windows Server-behållare, aktiverar du först *WindowsPreview* -funktions flaggorna i din prenumeration. *WindowsPreview* -funktionen använder också kluster med flera noder och skalnings uppsättningar för virtuella datorer för att hantera distributionen och konfigurationen av Kubernetes-noderna. Registrera funktions flaggan *WindowsPreview* med [funktions registrerings kommandot AZ][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Alla AKS-kluster som du skapar när du har registrerat funktions flaggan *WindowsPreview* använder du den här för hands versionen av klustret. För att fortsätta att skapa vanliga kluster som stöds fullständigt, aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat test-eller utvecklings-Azure-prenumeration för att testa för hands versions funktioner.

Det tar några minuter för registreringen att slutföras. Kontrol lera registrerings status med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

När registrerings statusen är `Registered`trycker du på CTRL-C för att stoppa övervakning av status.  Uppdatera sedan registreringen av *Microsoft. container service* Resource Provider med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera Node-pooler:

* Du kan inte ta bort den första noden.

När den här funktionen är i för hands version gäller följande ytterligare begränsningar:

* AKS-klustret kan ha högst åtta noder i pooler.
* AKS-klustret kan ha högst 400 noder i de åtta noderna i poolen.
* Namnet på Windows Server-adresspoolen innehåller högst 6 tecken.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resurs grupp med kommandot [AZ Group Create][az-group-create] .

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

> [!NOTE]
> I den här artikeln används bash syntax för kommandona i den här självstudien.
> Om du använder Azure Cloud Shell kontrollerar du att List rutan längst upp till vänster i Cloud Shells fönstret är inställd på **bash**.

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

För att kunna köra ett AKS-kluster som har stöd för resurspooler för Windows Server-behållare, måste klustret använda en nätverks princip som använder [Azure cni][azure-cni-about] (avancerat) nätverks-plugin. Mer detaljerad information om hur du planerar ut nödvändiga undernät och nätverks överväganden finns i [Konfigurera Azure cni Networking][use-advanced-networking]. Använd kommandot [AZ AKS Create][az-aks-create] för att skapa ett AKS-kluster med namnet *myAKSCluster*. Med det här kommandot skapas nödvändiga nätverks resurser om de inte finns.
  * Klustret har kon figurer ATS med två noder
  * Parametrarna *Windows-Admin-Password* och *Windows-Admin-username* anger admin-autentiseringsuppgifter för alla Windows Server-behållare som skapats i klustret.

> [!NOTE]
> För att klustret ska fungera på ett tillförlitligt sätt bör du köra minst två noder (två) i standardnodens pool.

Ange din egen säkra *PASSWORD_WIN* (kom ihåg att kommandona i den här artikeln har angetts i ett bash-gränssnitt):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Om du får ett lösen ords verifierings fel kan du försöka skapa en resurs grupp i en annan region.
> Försök sedan att skapa klustret med den nya resurs gruppen.

> [!Note]
> Om du inte kan skapa AKS-klustret eftersom versionen inte stöds i den här regionen kan du använda kommandot [AZ AKS get-versions-location öster] för att hitta den versions lista som stöds för den här regionen.


Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret. Ibland kan det ta längre tid än några minuter att etablera klustret. Tillåt upp till 10 minuter i dessa fall. 

## <a name="add-a-windows-server-node-pool"></a>Lägga till en pool för Windows Server-noder

Som standard skapas ett AKS-kluster med en Node-pool som kan köra Linux-behållare. Använd `az aks nodepool add` kommandot för att lägga till ytterligare en adresspool som kan köra Windows Server-behållare.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

Kommandot ovan skapar en ny Node-pool med namnet *npwin* och lägger till den i *myAKSCluster*. När du skapar en Node-pool för att köra Windows Server-behållare är standardvärdet för *Node-VM-size* *Standard_D2s_v3*. Om du väljer att ange parametern *Node-VM-size* kontrollerar du listan över [begränsade VM-storlekar][restricted-vm-sizes]. Den minsta rekommenderade storleken är *Standard_D2s_v3*. Kommandot ovan använder också standard under nätet i standard-VNet som skapas när du kör `az aks create`.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommando rads klient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Om du vill installera `kubectl` lokalt använder du kommandot [AZ AKS install-CLI][az-aks-install-cli] :

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

I följande exempel på utdata visas alla noder i klustret. Kontrol lera att status för alla noder är *klar*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Köra programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här artikeln används ett manifest för att skapa alla objekt som behövs för att köra ASP.NET-exempel programmet i en Windows Server-behållare. Det här manifestet innehåller en [Kubernetes-distribution][kubernetes-deployment] för exempel programmet ASP.net och en extern [Kubernetes-tjänst][kubernetes-service] för att komma åt programmet från Internet.

Exempel programmet ASP.NET tillhandahålls som en del av .NET Framework- [exempel][dotnet-samples] och körs i en Windows Server-behållare. AKS kräver att Windows Server-behållare baseras på avbildningar av *Windows server 2019* eller senare. Manifest filen Kubernetes måste också definiera en [Node-selektor][node-selector] som talar om för ditt AKS-kluster att köra ASP.NET-Pod på en nod som kan köra Windows Server-behållare.

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
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
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

Distribuera programmet med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```azurecli-interactive
kubectl apply -f sample.yaml
```

Följande exempel på utdata visar att distributionen och tjänsten har skapats:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra. Ibland kan tjänsten ta längre tid än några minuter att etablera. Tillåt upp till 10 minuter i dessa fall.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Inlednings vis visas den *externa IP-adressen* för *exempel* tjänsten som *väntar*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress, använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se exempel appen i praktiken öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild av bläddring till ASP.NET exempel program](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Om du får en tids gräns för anslutningen när du försöker läsa in sidan bör du kontrol lera att exempel appen är klar med följande kommando [kubectl get poddar--Watch]. Ibland kommer Windows-behållaren inte att startas när den externa IP-adressen är tillgänglig.

## <a name="delete-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, behållar tjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Anvisningar om hur du tar bort tjänstens huvud namn finns i [AKS Service Principal överväganden och borttagning][sp-delete].

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat ett Kubernetes-kluster och distribuerat ett ASP.NET exempel program i en Windows Server-behållare till det. [Få åtkomst till Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för det kluster som du nyss skapade.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [AKS-självstudie][aks-tutorial]

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
