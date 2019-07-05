---
title: Förhandsgranskning – och använda en Standard-SKU-belastningsutjämnare i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder en belastningsutjämnare med Standard-SKU för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476812"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Förhandsgranskning – och använda en Standard-SKU-belastningsutjämnare i Azure Kubernetes Service (AKS)

Du kan skapa och använda en Azure Load Balancer för att ge åtkomst till dina program i Azure Kubernetes Service (AKS). En belastningsutjämnare som körs på AKS kan användas som en intern eller en extern belastningsutjämnare. En intern belastningsutjämnare gör en Kubernetes-tjänst som är tillgängligt endast för program som körs i samma virtuella nätverk som AKS-klustret. En extern belastningsutjämnare tar emot en eller flera offentliga IP-adresser för inkommande och gör en Kubernetes-tjänst som är tillgänglig externt med hjälp av den offentliga IP-adresser.

Azure Load Balancer är tillgängliga i två SKU: er - *grundläggande* och *Standard*. Som standard den *grundläggande* SKU som ska användas när ett tjänstmanifest används för att skapa en belastningsutjämnare på AKS. Med hjälp av en *Standard* belastningsutjämnaren med SKU ger ytterligare funktioner och funktioner, till exempel större för backend-poolen och Tillgänglighetszoner. Det är viktigt att du förstår skillnaderna mellan *Standard* och *grundläggande* belastningsutjämnare innan du väljer att använda. När du skapar ett AKS-kluster kan ändra du inte belastningsutjämnaren SKU för klustret. Mer information om den *grundläggande* och *Standard* SKU: er, se [Azure load balancer SKU jämförelse][azure-lb-comparison].

Den här artikeln visar hur du skapar och använder en Azure-belastningsutjämnare med den *Standard* SKU med Azure Kubernetes Service (AKS).

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes och Azure Load Balancer-koncept. Mer information finns i [Kubernetes viktiga begrepp för Azure Kubernetes Service (AKS)][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Den här funktionen är för närvarande en förhandsversion.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Innan du börjar

AKS-kluster tjänstens huvudnamn måste behörighet att hantera nätverksresurser om du använder ett befintligt undernät eller en resursgrupp. I allmänhet tilldela den *nätverksdeltagare* roll till tjänstens huvudnamn på delegerade resurser. Mer information om behörigheter finns i [AKS delegera åtkomst till andra Azure-resurser][aks-sp].

Du måste skapa ett AKS-kluster som anger SKU: N för att belastningsutjämnaren ska *Standard* istället för standardvärdet *grundläggande*. Skapa ett AKS-kluster täcks i ett senare steg, men du måste först aktivera några funktioner i förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning, delta i. De tillhandahålls för att samla in feedback och buggar från vår community. I förhandsversionen kan är inte dessa funktioner avsedda för användning i produktion. Funktioner i offentliga förhandsversioner omfattas ”bästa prestanda” support. Hjälp från teamen för AKS-teknisk support är tillgänglig under kontorstid Pacific tidszon (Stillahavstid) endast. Mer information finns i följande supportartiklar:
>
> * [AKS supportprinciper][aks-support-policies]
> * [Vanliga frågor om Azure-Support][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för aks-förhandsversion

Du använder Azure load balancer standard-SKU som du behöver den *aks-förhandsversion* CLI tilläggsversion 0.4.1 eller högre. Installera den *förhandsversionen av aks* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] kommandot::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Registrera AKSAzureStandardLoadBalancer förhandsgranskningsfunktion

Skapa ett AKS-kluster som kan använda en belastningsutjämnare med den *Standard* SKU, måste du aktivera den *AKSAzureStandardLoadBalancer* funktionen flaggan för din prenumeration. Den *AKSAzureStandardLoadBalancer* även funktionen använder *VMSSPreview* när du skapar ett kluster med VM-skalningsuppsättningar. Den här funktionen innehåller den senaste uppsättningen förbättringar av tjänsten när du konfigurerar ett kluster. Även om det inte krävs, bör du aktivera den *VMSSPreview* funktionen flaggan också.

> [!CAUTION]
> När du registrerar en funktion i en prenumeration kan du inte för närvarande avregistrera den funktionen. När du aktiverar vissa funktioner i förhandsversion, kan standardinställningar användas för alla AKS-kluster som skapas i prenumerationen. Inte aktivera förhandsversionsfunktioner för produktion-prenumerationer. Använd en separat prenumeration för att testa funktioner och samla in feedback.

Registrera den *VMSSPreview* och *AKSAzureStandardLoadBalancer* funktionen flaggar med hjälp av den [az funktionen registrera][az-feature-register] kommandot som visas i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Alla AKS-kluster som du skapar när du har registrerat den *VMSSPreview* eller *AKSAzureStandardLoadBalancer* funktionen flaggor använda förhandsupplevelsen för klustret. Om du vill fortsätta att skapa kluster av regelbundna, fullt stöd inte aktivera förhandsversionsfunktioner för produktion-prenumerationer. Använd en separat test- eller Azure-prenumeration för att testa förhandsversionsfunktioner.

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan][az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som har stöd för en belastningsutjämnare med den *Standard* SKU:

* När du använder den *Standard* SKU för en belastningsutjämnare, måste du tillåta offentliga adresser och undvika att skapa alla Azure-princip som förbud IP skapas. AKS-klustret skapar automatiskt en *Standard* SKU offentlig IP-adress i samma resursgrupp som skapats för AKS-kluster, vilket vanligtvis namnges med *MC_* i början. AKS tilldelar den offentliga IP-Adressen till den *Standard* SKU belastningsutjämnare. Offentlig IP-adress krävs för att tillåta utgående trafik från AKS-klustret. Den här offentliga IP-adress krävs också för att upprätthålla anslutningen mellan kontroll plan och agenten noderna samt att bibehålla kompatibilitet med tidigare versioner av AKS.
* När du använder den *Standard* SKU för en belastningsutjämnare måste du använda Kubernetes version 1.13.5 eller större.

Den här funktionen är i förhandsversion, begränsningar gäller följande ytterligare:

* När du använder den *Standard* SKU för en belastningsutjämnare i AKS, du kan inte ange din egna offentliga IP-adress för utgående trafik för belastningsutjämnaren. Du måste använda IP-adressen AKS tilldelas till belastningsutjämnaren.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är där resource group metadata lagras, det är också där dina resurser kör i Azure om du inte anger en annan region under resursskapandet. Skapa en resurs med det [az gruppen skapa][az-group-create] kommando.

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
För att köra ett AKS-kluster som har stöd för en belastningsutjämnare med den *Standard* SKU, ditt kluster måste ange den *load Balancers sku* parameter *standard*. Den här parametern skapar en belastningsutjämnare med den *Standard* SKU när klustret skapas. När du kör en *LoadBalancer* på ditt kluster, konfigurationen av den *Standard* SK belastningsutjämnaren uppdateras med tjänstens konfiguration. Använd den [az aks skapa][az-aks-create] kommando för att skapa ett AKS-kluster med namnet *myAKSCluster*.

> [!NOTE]
> Den *load Balancers sku* egenskapen kan bara användas när klustret skapas. Du kan inte ändra belastningsutjämnaren SKU när du har skapat ett AKS-kluster. Dessutom kan du bara använda en typ av belastningsutjämnare SKU: N i ett enda kluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

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
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Kontrollera att klustret använder den *Standard* SKU

Använd den [az aks show][az-aks-show] att visa konfigurationen för klustret.

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

Kontrollera den *loadBalancerSku* egenskapen visas som *standard*.

## <a name="use-the-load-balancer"></a>Använd belastningsutjämnaren

Om du vill använda belastningsutjämnaren i ditt kluster, kan du skapa ett tjänstmanifest med tjänsttypen *LoadBalancer*. Skapa en annan manifest för att visa belastningsutjämnaren fungerar, med ett exempelprogram som ska köras på klustret. Det här exempelprogrammet exponeras via belastningsutjämnaren och kan ses via en webbläsare.

Skapa ett manifest med namnet `sample.yaml` som visas i följande exempel:

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

Ovanstående manifestet konfigurerar två distributioner: *azure-vote-front* och *bak-azure-vote*. Konfigurera *azure-vote-front* distributionen ska vara tillgängliga via belastningsutjämnaren, skapa ett manifest med namnet `standard-lb.yaml` som visas i följande exempel:

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

Tjänsten *azure-vote-front* använder den *LoadBalancer* Skriv för att konfigurera belastningsutjämnaren på AKS-kluster för att ansluta till den *azure-vote-front* distribution.

Distribuera exempelprogrammet och läsa in belastningsutjämnare med hjälp av den [kubectl gäller][kubectl-apply] och ange namnet på din YAML-manifest:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

Den *Standard* belastningsutjämnaren med SKU har nu konfigurerats för att exponera exempelprogrammet. Visa tjänstinformation om *azure-vote-front* med [kubectl få][kubectl-get] att se den offentliga IP-Adressen för belastningsutjämnaren. Den offentliga IP-adressen för belastningsutjämnaren visas i den *extern IP-adress* kolumn. Det kan ta en minut eller två IP-adressen ska ändras från *\<väntande\>* en faktiska externa IP-adress, som visas i följande exempel:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Navigera till den offentliga IP-Adressen i en webbläsare och kontrollera att du ser exempelprogrammet. I exemplet ovan är den offentliga IP-Adressen `52.179.23.131`.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Du kan också konfigurera en belastningsutjämnare för att vara intern och inte exponera en offentlig IP-adress. Om du vill konfigurera belastningsutjämnaren som internt, lägger du till `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` som en anteckning till den *LoadBalancer* service. Du kan se ett exempel yaml manifest som också mer information om en intern belastningsutjämnare [här][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Rensa konfigurationen för belastningsutjämnaren på Standard-SKU

Om du vill ta bort konfigurationen för exemplet program- och load balancer använder [kubectl ta bort][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes-tjänster på den [dokumentation för Kubernetes services][kubernetes-services].

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
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
