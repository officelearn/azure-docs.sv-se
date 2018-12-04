---
title: Skapa virtuella noder med hjälp av portalen i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure-portalen för att skapa ett kluster i Azure Kubernetes Services (AKS) som använder virtuella noder för att köra poddar.
services: container-service
author: iainfoulds
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 24b35f3cd0c1dc7d78832c42c3cc4f1608f73bfb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856730"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Skapa och konfigurera en Azure Kubernetes Services kluster (AKS) för att använda virtuella noder i Azure portal

För att snabbt distribuera arbetsbelastningar i ett kluster i Azure Kubernetes Service (AKS), kan du använda virtuella noder. Med virtuella noder har snabb etablering av poddar och betala bara per sekund för sin Utförandetid. I ett scenario med skalning behöver du inte vänta tills autoskalningen för Kubernetes-klustret att distribuera VM-beräkningsnoder för att köra de nya poddarna. Den här artikeln visar hur du skapar och konfigurerar resurser för virtuella nätverk och ett AKS-kluster med virtuella noder som är aktiverad.

> [!IMPORTANT]
> Virtuella noder för AKS är för närvarande i **förhandsversion**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Längst upp i vänstra hornet i Azure-portalen väljer du **Skapa en resurs** > **Kubernetes Service**.

På den **grunderna** konfigurerar följande alternativ:

- *PROJEKTINFORMATION*: Välj en Azure-prenumeration och välj sedan eller skapa en Azure-resursgrupp, till exempel *myResourceGroup*. Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*.
- *KLUSTERINFORMATION*: Välj en region, en Kubernetes-version och ett DNS-namnprefix för AKS-klustret.
- *SKALA*: Välj en storlek på virtuell dator för AKS-noderna. VM-storleken **kan inte** ändras efter att ett AKS-kluster har distribuerats.
    - Välj även det antal noder som ska distribueras till klustret. Den här artikeln är att ställa in **nodantal** till *1*. Antalet noder **kan** justeras efter att klustret har distribuerats.
    - Under **virtuella noder**väljer *aktiverad*.

![Skapa AKS-kluster och aktivera virtuella noder](media/virtual-nodes-portal/enable-virtual-nodes.png)

Som standard skapas en Azure Active Directory-tjänstens huvudnamn. Den här tjänstens huvudnamn används för klusterkommunikation och integrering med andra Azure-tjänster.

Klustret konfigureras också avancerade nätverk. Virtuella noder är konfigurerade för att använda sina egna Azure-nätverksundernät. Det här undernätet har delegerats behörighet att ansluta Azure-resurser mellan AKS-klustret. Om du inte redan har delegerade undernät, Azure-portalen skapar och konfigurerar Azure-nätverk och undernät för användning med virtuella noder.

Välj **Granska + skapa**. När verifieringen är klar, Välj **skapa**.

Det tar några minuter för AKS-klustret att skapas och bli redo för användning.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Hantera Kubernetes-kluster med [kubectl][kubectl], Kubernetes kommandoradsklient. `kubectl`-klienten är förinstallerad i Azure Cloud Shell.

Om du vill öppna Cloud Shell, Välj **prova** från det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Använd kommandot [az aks get-credentials][az-aks-get-credentials] för att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster. I följande exempel hämtas autentiseringsuppgifterna för klusternamnet *myAKSCluster* i den resursgrupp som heter *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande Exempelutdata visar den enskilda VM-noden som skapats och sedan noden virtuella för Linux, *virtuella-nod-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempelapp

I Azure Cloud Shell, skapar du en fil med namnet `virtual-node.yaml` och kopiera följande YAML. Så här schemalägger du behållaren på noden och en [nodeSelector] [ node-selector] och [toleration] [ toleration] har definierats. De här inställningarna gör det möjligt för poden att schemaläggas på den virtuella noden och bekräfta att funktionen har aktiverats.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Kör programmet med den [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Använd den [kubectl hämta poddar] [ kubectl-get] med den `-o wide` argumentet att mata ut en lista över poddar och noden schemalagda. Observera att den `virtual-node-helloworld` pod har schemalagts på den `virtual-node-linux` noden.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Poden tilldelas en intern IP-adress från Azure-nätverksundernät delegerad för användning med virtuella noder.

## <a name="test-the-virtual-node-pod"></a>Testa virtuell nod pod

Bläddra till demoprogram med en webbklient för att testa pod körs på virtuella noden. Eftersom en intern IP-adress tilldelas i en pod, kan du snabbt testa den här anslutningen från en annan pod på AKS-klustret. Skapa en test-pod och bifogas en terminalsession:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Installera `curl` i en pod med hjälp av `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Nu komma åt adressen för din pod med hjälp av `curl`, till exempel *http://10.241.0.4*. Ange din egen interna IP-adress som visas i föregående `kubectl get pods` kommando:

```azurecli-interactive
curl -L http://10.241.0.4
```

Demoprogrammet visas enligt följande komprimerade exempel på utdata:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalsession till din test pod med `exit`. När sessionen avslutas är poden den tagits bort.

## <a name="next-steps"></a>Nästa steg

I den här artikeln var en pod schemalagda på virtuell nod och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänstdistribution och dirigera trafik till din pod via en belastningsutjämnare eller ingress-kontrollant. Mer information finns i [skapa en grundläggande ingress-kontrollant i AKS][aks-basic-ingress].

Virtuella noder är en komponent i en skalning lösning i AKS. Mer information om skalning lösningar finns i följande artiklar:

- [Använda Kubernetes vågrät pod autoskalningen][aks-hpa]
- [Använda Kubernetes-kluster autoskalningen][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
