---
title: Använda en standard-SKU-belastningsutjämnare i Azure Kubernetes service (AKS)
description: Lär dig hur du använder en belastningsutjämnare med en standard-SKU för att exponera dina tjänster med Azure Kubernetes service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: 23cbab927c994024ba733a0cfd7a15c58a2970a7
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861285"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Använda en standard-SKU-belastningsutjämnare i Azure Kubernetes service (AKS)

För att ge åtkomst till dina program i Azure Kubernetes service (AKS) kan du skapa och använda en Azure Load Balancer. En belastningsutjämnare som körs på AKS kan användas som en intern eller extern belastningsutjämnare. En intern belastningsutjämnare gör att en Kubernetes-tjänst endast är tillgänglig för program som körs i samma virtuella nätverk som AKS-klustret. En extern belastningsutjämnare tar emot en eller flera offentliga IP-adresser för ingress och gör en Kubernetes-tjänst tillgänglig externt med hjälp av offentliga IP-adresser.

Azure Load Balancer finns i två SKU: er – *Basic* och *standard*. Som standard används *Basic* SKU när ett tjänst manifest används för att skapa en BELASTNINGSUTJÄMNARE på AKS. Med en *standard* -SKU-belastningsutjämnare får du ytterligare funktioner och funktioner, till exempel större storlek på backend-pool och Tillgänglighetszoner. Det är viktigt att du förstår skillnaderna mellan *standard* -och *grundläggande* belastningsutjämnare innan du väljer vilken du vill använda. När du har skapat ett AKS-kluster kan du inte ändra SKU för belastnings utjämning för det klustret. Mer information om *Basic* -och *standard* -SKU: er finns i [jämförelse av Azure Load Balancer SKU][azure-lb-comparison].

Den här artikeln visar hur du skapar och använder en Azure Load Balancer med *standard* -SKU: n med Azure Kubernetes service (AKS).

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes och Azure Load Balancer koncept. Mer information finns i [Kubernetes Core Concepts for Azure Kubernetes service (AKS)][kubernetes-concepts] och [Vad är Azure Load Balancer?][azure-lb].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Innan du börjar

AKS-kluster tjänstens huvud namn måste ha behörighet att hantera nätverks resurser om du använder ett befintligt undernät eller en befintlig resurs grupp. I allmänhet tilldelar du rollen *nätverks deltagare* till tjänstens huvud namn på de delegerade resurserna. Mer information om behörigheter finns i [delegera AKS-åtkomst till andra Azure-resurser][aks-sp].

Du måste skapa ett AKS-kluster som anger SKU: n för belastningsutjämnaren till *standard* i stället för standard *Basic*.

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill använda standard-SKU: n för Azure Load Balancer behöver du *AKS-Preview CLI-* tillägg version 0.4.12 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder en belastningsutjämnare med *standard* -SKU: n:

* Minst en offentlig IP-adress eller ett IP-prefix krävs för att tillåta utgående trafik från AKS-klustret. Det offentliga IP-eller IP-prefixet krävs också för att upprätthålla anslutningen mellan kontroll planet och agent-noder och för att upprätthålla kompatibilitet med tidigare versioner av AKS. Du kan välja mellan följande alternativ för att ange offentliga IP-adresser eller IP-prefix med en *standard* -SKU-belastningsutjämnare:
    * Ange dina egna offentliga IP-adresser.
    * Ange egna offentliga IP-prefix.
    * Ange ett tal upp till 100 för att tillåta att AKS-klustret skapar att många *standard* -SKU: er i samma resurs grupp som skapas som AKS-kluster, som vanligt vis heter med *MC_* i början. AKS tilldelar den offentliga IP-adressen till *standard* -SKU-belastningsutjämnaren. Som standard skapas en offentlig IP-adress automatiskt i samma resurs grupp som AKS-klustret, om ingen offentlig IP, ett offentligt IP-prefix eller antal IP-adresser anges. Du måste också tillåta offentliga adresser och undvika att skapa Azure Policy som tillåter att IP skapas.
* När du använder *standard* -SKU: n för en belastningsutjämnare måste du använda Kubernetes version 1,13 eller senare.
* Du kan bara definiera belastningsutjämnare-SKU: n när du skapar ett AKS-kluster. Du kan inte ändra SKU: n för belastningsutjämnaren efter att ett AKS-kluster har skapats.
* Du kan bara använda en SKU för belastnings utjämning i ett enda kluster.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resurs grupp med kommandot [AZ Group Create][az-group-create] .

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

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

## <a name="create-aks-cluster"></a>Skapa AKS-kluster
För att kunna köra ett AKS-kluster som har stöd för en belastningsutjämnare med *standard* -SKU: n, måste klustret ange parametern *Load-Balancer-SKU* till *standard*. Den här parametern skapar en belastningsutjämnare med *standard* -SKU: n när klustret skapas. När du kör en *Loadbalancer* -tjänst i klustret uppdateras konfigurationen av *standard* -SKU-belastningsutjämnaren med tjänstens konfiguration. Använd kommandot [AZ AKS Create][az-aks-create] för att skapa ett AKS-kluster med namnet *myAKSCluster*.

> [!NOTE]
> Egenskapen *Load-Balancer-SKU* kan endast användas när klustret skapas. Du kan inte ändra SKU: n för belastningsutjämnaren efter att ett AKS-kluster har skapats. Du kan också bara använda en typ av SKU för belastningsutjämnare i ett enda kluster.
> 
> Om du vill använda dina egna offentliga IP-adresser använder du parametrarna *Load-Balancer-utgående-IP*eller *Load-Balancer-utgående-IP-prefix* . Båda parametrarna kan också användas vid [uppdatering av klustret](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommando rads klient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Installera `kubectl` lokalt genom att använda kommandot [AZ AKS install-CLI][az-aks-install-cli] :

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
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Verifiera att klustret använder *standard* -SKU: n

Använd [AZ AKS show][az-aks-show] för att visa konfigurationen av klustret.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Kontrol lera att egenskapen *loadBalancerSku* visar som *standard*.

## <a name="use-the-load-balancer"></a>Använda belastningsutjämnaren

Om du vill använda belastnings utjämning i klustret skapar du ett tjänst manifest med tjänst typen *Loadbalancer*. Om du vill visa belastnings Utjämnings arbetet skapar du ett annat manifest med ett exempel program som ska köras i klustret. Det här exempel programmet exponeras via belastningsutjämnaren och kan visas via en webbläsare.

Skapa ett manifest som `sample.yaml` heter som det visas i följande exempel:

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
```

Ovanstående manifest konfigurerar två distributioner: *Azure-rösta-framtill* och *Azure-rösta – tillbaka*. Om du vill konfigurera *Azure-röst-front-* distribution som ska exponeras med belastningsutjämnaren skapar du ett manifest `standard-lb.yaml` som heter som visas i följande exempel:

```yaml
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

Tjänsten *Azure-röst-front* använder *Loadbalancer* -typen för att konfigurera belastningsutjämnaren på ditt AKS-kluster för att ansluta till *Azure-röstning-frontend-* distributionen.

Distribuera exempel programmet och belastningsutjämnaren med hjälp av [kubectl tillämpa][kubectl-apply] och ange namnet på dina yaml-manifest:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*Standard* -SKU: n är nu konfigurerad för att exponera exempel programmet. Visa tjänst informationen för *Azure-röstning-frontend* med [kubectl Hämta][kubectl-get] för att se belastnings UTJÄMNINGENS offentliga IP-adress. Belastnings utjämningens offentliga IP-adress visas i kolumnen *extern IP-* adress. Det kan ta en minut eller två för IP-adressen att ändras från *\<väntar\>* till en faktisk extern IP-adress, som visas i följande exempel:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Navigera till den offentliga IP-adressen i en webbläsare och kontrol lera att du ser exempel programmet. I exemplet ovan är `52.179.23.131`den offentliga IP-adressen.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Du kan också konfigurera belastningsutjämnaren så att den är intern och inte exponera en offentlig IP-adress. Om du vill konfigurera belastningsutjämnaren som intern lägger du till `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` som en anteckning till *Loadbalancer* -tjänsten. Du kan se ett exempel på yaml-manifest och mer information om en intern belastningsutjämnare [här][internal-lb-yaml].

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Valfria – skala antalet hanterade offentliga IP-adresser

När du använder en *standard* -SKU för belastningsutjämnare med hanterade utgående offentliga IP-adresser, som skapas som standard, kan du skala antalet hanterade utgående offentliga IP-adresser med hjälp av parametern *Load-Balancer-Managed-IP-Count* .

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Exemplet ovan anger antalet hanterade utgående offentliga IP-adresser till *2* för *myAKSCluster* -klustret i *myResourceGroup*. Du kan också använda parametern *belastningsutjämnare-hanterade-IP-antal* för att ange det ursprungliga antalet hanterade utgående offentliga IP-adresser när du skapar klustret. Standardvärdet för hanterade utgående offentliga IP-adresser är 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Valfritt – ange egna offentliga IP-adresser eller prefix för utgående trafik

När du använder en *standard* -SKU-BELASTNINGSUTJÄMNARE skapar AKS-klustret automatiskt en offentlig IP-adress i samma resurs grupp som skapats för AKS-klustret och tilldelar den offentliga IP-adressen till *standard* -SKU-belastningsutjämnaren. Alternativt kan du tilldela din egen offentliga IP-adress.

> [!IMPORTANT]
> Du måste använda *standard* SKU offentliga IP: er för utgående med din *standard* -SKU. Du kan kontrol lera SKU: er för dina offentliga IP-adresser med hjälp av kommandot [AZ Network Public-IP show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Använd kommandot [AZ Network Public-IP show][az-network-public-ip-show] för att visa ID: n för dina offentliga IP-adresser.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Kommandot ovan visar ID: t för *myPublicIP* offentliga IP i resurs gruppen *myResourceGroup* .

Använd kommandot *AZ AKS Update* med parametern *Load-Balancer-utgående-IP* för att uppdatera ditt kluster med dina offentliga IP-adresser.

I följande exempel används parametern *Load-Balancer-utgående-IP* med ID: n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Du kan också använda offentliga IP-prefix för utgående trafik med SKU: n för *standard* -SKU. I följande exempel används kommandot [AZ Network Public-IP prefixet show][az-network-public-ip-prefix-show] för att visa en lista med ID: n för dina offentliga IP-prefix:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Kommandot ovan visar ID: t för det offentliga IP-prefixet *myPublicIPPrefix* i resurs gruppen *myResourceGroup* .

Använd kommandot *AZ AKS Update* med parametern *Load-Balancer-utgående-IP-prefix* med ID: n från föregående kommando.

I följande exempel används parametern *Load-Balancer-utgående-IP-prefix* med ID: n från föregående kommando.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> De offentliga IP-adresserna och IP-prefixen måste finnas i samma region och ingå i samma prenumeration som ditt AKS-kluster.

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Rensa standard konfigurationen för SKU-belastnings utjämning

Ta bort exempel programmet och belastnings Utjämnings konfigurationen med [kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes Services i [dokumentationen för Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

