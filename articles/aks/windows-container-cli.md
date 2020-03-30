---
title: Preview - Skapa en Windows Server-behållare i ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster och distribuerar ett program i en Windows Server-behållare i Azure Kubernetes Service (AKS) med Hjälp av Azure CLI.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 259728da5ea7f71110ce183ae25bb47a0f873614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475518"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Preview - Skapa en Windows Server-behållare på ett AKS-kluster (Azure Kubernetes Service) med Hjälp av Azure CLI

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här artikeln distribuerar du ett AKS-kluster med Hjälp av Azure CLI. Du kan också distribuera ett ASP.NET exempelprogram i en Windows Server-behållare till klustret.

Den här funktionen är för närvarande en förhandsversion.

![Bild av att surfa till ASP.NET exempelprogram](media/windows-container/asp-net-sample-app.png)

Denna artikel förutsätter en grundläggande förståelse av Kubernetes begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0.61 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="before-you-begin"></a>Innan du börjar

Du måste lägga till ytterligare en nodpool när du har skapat klustret som kan köra Windows Server-behållare. Att lägga till ytterligare en nodpool täcks i ett senare steg, men du måste först aktivera några förhandsgranskningsfunktioner.

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är självbetjäningsanmälan. Förhandsvisningar tillhandahålls "i nu och "som tillgängligt" och är undantagna från servicenivåavtalen och den begränsade garantin. AKS-förhandsvisningar omfattas delvis av kundsupport efter bästa ansträngning. Därför är dessa funktioner inte avsedda för produktionsanvändning. För ytterligare infromation, se följande supportartiklar:
>
> * [Aks-supportpolicyer][aks-support-policies]
> * [Vanliga frågor och svar om Azure-support][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för förhandsversion

Om du vill använda Windows Server-behållare behöver du VERSION 0.4.12 eller senare för förhandsversion av *CLI-versionen* 0.4.12. Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add][az-extension-add] och sök sedan efter tillgängliga uppdateringar med kommandot az extension [update::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Förhandsgranskningsfunktionen Registrera Windows

Om du vill skapa ett AKS-kluster som kan använda flera nodpooler och köra Windows Server-behållare aktiverar du först WindowsPreview-funktionsflaggorna för din prenumeration. *WindowsPreview* *Funktionen WindowsPreview* använder också poolkluster med flera noder och skalningsuppsättning för virtuella datorer för att hantera distributionen och konfigurationen av Kubernetes-noderna. Registrera funktionsflaggan *för WindowsPreview* med kommandot [az-funktionsregister][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Alla AKS-kluster som du skapar när du har registrerat flaggan *Förhandsgranskningsfunktion* använder den här klusterupplevelsen för förhandsversionen. Om du vill fortsätta att skapa vanliga, fullt stödda kluster aktiverar du inte förhandsgranskningsfunktioner på produktionsprenumerationer. Använd en separat Azure-prenumeration för testning av förhandsversioner av test eller utveckling för att testa förhandsversionsfunktioner.

Det tar några minuter innan registreringen är klar. Kontrollera registreringsstatus med kommandot [az-funktionslista:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

När registreringstillståndet är `Registered`trycker du på Ctrl-C för att stoppa övervakningen av tillståndet.  Uppdatera sedan registreringen av *microsoft.ContainerService-resursprovidern* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera nodpooler:

* Du kan inte ta bort den första nodpoolen.

Även om den här funktionen är i förhandsversion gäller följande ytterligare begränsningar:

* AKS-klustret kan ha högst åtta nodpooler.
* AKS-klustret kan ha högst 400 noder över dessa åtta nodpooler.
* Poolnamnet för Windows Server-nod har en gräns på 6 tecken.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är där metadata för resursgrupp lagras, det är också där dina resurser körs i Azure om du inte anger en annan region när du skapar resurser. Skapa en resursgrupp med hjälp av kommandot [az group create][az-group-create].

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

> [!NOTE]
> I den här artikeln används bassyntaxen för kommandona i den här självstudien.
> Om du använder Azure Cloud Shell kontrollerar du att listrutan längst upp till vänster i Cloud Shell-fönstret är inställd på **Bash**.

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

För att kunna köra ett AKS-kluster som stöder nodpooler för Windows Server-behållare måste klustret använda en nätverksprincip som använder [Azure CNI-plugin-program][azure-cni-about] (avancerat). Mer detaljerad information för att planera de nödvändiga undernätsområdena och nätverksövervägandena finns i [konfigurera Azure CNI-nätverk][use-advanced-networking]. Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster med namnet *myAKSCluster*. Det här kommandot skapar nödvändiga nätverksresurser om de inte finns.
  * Klustret är konfigurerat med två noder
  * Parametrarna *windows-admin-password* och *windows-admin-username* anger administratörsautentiseringsuppgifter för alla Windows Server-behållare som skapats i klustret.

> [!NOTE]
> Om du vill att klustret ska fungera tillförlitligt bör du köra minst 2 (två) noder i standardnodpoolen.

Ange din egen säkra *PASSWORD_WIN* (kom ihåg att kommandona i den här artikeln är inskrivna i ett BASH-skal):

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
> Om du får ett lösenordsverifieringsfel kan du prova att skapa resursgruppen i en annan region.
> Försök sedan att skapa klustret med den nya resursgruppen.

> [!Note]
> Om du inte kan skapa AKS-klustret eftersom versionen inte stöds i den här regionen kan du använda kommandot [az aks get-versions --location eastus] för att hitta den versionslista som stöds för den här regionen.


Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret. Ibland kan klustret ta längre tid än några minuter att etablera. Tillåt upp till 10 minuter i dessa fall. 

## <a name="add-a-windows-server-node-pool"></a>Lägga till en Windows Server-nodpool

Som standard skapas ett AKS-kluster med en nodpool som kan köra Linux-behållare. Använd `az aks nodepool add` kommandot om du vill lägga till ytterligare en nodpool som kan köra Windows Server-behållare.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

Kommandot ovan skapar en ny nodpool med namnet *npwin* och lägger till den *i myAKSCluster*. När du skapar en nodpool för att köra Windows Server-behållare är standardvärdet för *nod-vm-storlek* *Standard_D2s_v3*. Om du väljer att ange parametern *nod-vm-storlek* kontrollerar du listan över [begränsade vm-storlekar][restricted-vm-sizes]. Den minsta rekommenderade storleken är *Standard_D2s_v3*. Kommandot Ovan använder också standardundernätet i det virtuella `az aks create`standardnät som skapas när du kör .

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempelutdata visar alla noder i klustret. Kontrollera att statusen för alla noder är *Klar:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Köra appen

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här artikeln används ett manifest för att skapa alla objekt som behövs för att köra ASP.NET exempelprogram i en Windows Server-behållare. Det här manifestet innehåller en [Kubernetes-distribution][kubernetes-deployment] för ASP.NET exempelprogram och en extern [Kubernetes-tjänst][kubernetes-service] för att komma åt programmet från Internet.

Exempelprogrammet ASP.NET tillhandahålls som en del av [.NET Framework Samples][dotnet-samples] och körs i en Windows Server-behållare. AKS kräver att Windows Server-behållare baseras på bilder av *Windows Server 2019* eller senare. Kubernetes-manifestfilen måste också definiera en [nodväljare][node-selector] för att styra AKS-klustret att köra ASP.NET exempelprogrammets pod på en nod som kan köra Windows Server-behållare.

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

Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f sample.yaml
```

Följande exempelutdata visar den distribution och tjänst som har skapats:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra. Ibland kan tjänsten ta längre tid än några minuter att etablera. Tillåt upp till 10 minuter i dessa fall.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```console
kubectl get service sample --watch
```

Inledningsvis visas *EXTERN IP* för *exempeltjänsten* som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se exempelappen i praktiken öppnar du en webbläsare till tjänstens externa IP-adress.

![Bild av att surfa till ASP.NET exempelprogram](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Om du får en tidsgränsen för anslutningen när du försöker läsa in sidan bör du kontrollera att exempelappen är klar med följande kommando [kubectl get pods --watch]. Ibland kommer windows-behållaren inte att startas när din externa IP-adress är tillgänglig.

## <a name="delete-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat ett Kubernetes-kluster och distribuerat ett ASP.NET exempelprogram i en Windows Server-behållare till det. [Öppna Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för det kluster som du nyss skapade.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

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
