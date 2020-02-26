---
title: Skapa virtuella noder med portalen i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure Portal för att skapa ett AKS-kluster (Azure Kubernetes Services) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 664bbdc94963b84e4fed6845dfd23d2407ca3898
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592568"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Skapa och konfigurera ett Azure Kubernetes Services-kluster (AKS) för att använda virtuella noder i Azure Portal

Om du snabbt vill distribuera arbets belastningar i ett Azure Kubernetes service-kluster (AKS) kan du använda virtuella noder. Med virtuella noder har du snabb etablering av poddar och betalar bara per sekund för körnings tiden. I ett skalnings scenario behöver du inte vänta på att Kubernetes-klustret ska distribuera virtuella dator beräknings noder för att köra ytterligare poddar. Virtuella noder stöds bara med Linux-poddar och noder.

Den här artikeln visar hur du skapar och konfigurerar virtuella nätverks resurser och ett AKS-kluster med virtuella noder aktiverade.

## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett virtuellt nätverks under nät och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med *avancerade* nätverksfunktioner. Som standard skapas AKS-kluster med *grundläggande* nätverk. Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och sedan distribuerar ett AKS-kluster som använder avancerade nätverk.

Om du inte tidigare har använt ACI registrerar du tjänst leverantören med din prenumeration. Du kan kontrol lera statusen för registreringen av ACI-providern med kommandot [AZ Provider List][az-provider-list] , som du ser i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft. ContainerInstance* -providern ska rapportera som *registrerad*, vilket visas i följande exempel på utdata:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Om providern visas som *NotRegistered*registrerar du providern med [AZ-providerns register][az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regional tillgänglighet

Följande regioner stöds för distribution av virtuella noder:

* Östra Australien (australiaeast)
* Centrala USA (centrala)
* USA, östra (östra)
* USA, östra 2 (eastus2)
* Japan, östra (japanöstra)
* Nord Europa (europanorra)
* Sydostasien (Sydostasien)
* USA, västra centrala (westcentralus)
* Västeuropa (westeurope)
* USA, västra (väst)
* USA, västra 2 (westus2)

## <a name="known-limitations"></a>Kända begränsningar
Funktioner för virtuella noder är kraftigt beroende av ACI funktions uppsättning. Följande scenarier stöds inte ännu med virtuella noder

* Använda tjänstens huvud namn för att hämta ACR-avbildningar. [Lösning](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) är att använda [Kubernetes hemligheter](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Virtual Network begränsningar](../container-instances/container-instances-vnet.md) , inklusive VNet-peering, Kubernetes nätverks principer och utgående trafik till Internet med nätverks säkerhets grupper.
* Init-behållare
* [Värd Ali Aset](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argument](../container-instances/container-instances-exec.md#restrictions) för exec i Aci
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) kommer inte att distribuera poddar till den virtuella noden
* [Windows Server-noder (för närvarande i för hands version i AKS)](windows-container-cli.md) stöds inte tillsammans med virtuella noder. Du kan använda virtuella noder för att schemalägga Windows Server-behållare utan att behöva använda Windows Server-noder i ett AKS-kluster.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Längst upp i vänstra hornet i Azure-portalen väljer du **Skapa en resurs** > **Kubernetes Service**.

På sidan **grundläggande** inställningar konfigurerar du följande alternativ:

- *PROJEKTINFORMATION*: Välj en Azure-prenumeration och välj sedan eller skapa en Azure-resursgrupp, till exempel *myResourceGroup*. Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*.
- *KLUSTERINFORMATION*: Välj en region, en Kubernetes-version och ett DNS-namnprefix för AKS-klustret.
- *Primär Node-pool*: Välj en VM-storlek för AKS-noderna. VM-storleken **kan inte** ändras efter att ett AKS-kluster har distribuerats.
     - Välj även det antal noder som ska distribueras till klustret. I den här artikeln ställer du in **antal noder** på *1*. Antalet noder **kan** justeras efter att klustret har distribuerats.

Klicka på **Nästa: skala**.

På sidan **skala** väljer du *aktive rad* under **virtuella noder**.

![Skapa AKS-kluster och aktivera de virtuella noderna](media/virtual-nodes-portal/enable-virtual-nodes.png)

Som standard skapas en Azure Active Directory tjänstens huvud namn. Tjänstens huvud namn används för kluster kommunikation och integrering med andra Azure-tjänster.

Klustret är också konfigurerat för avancerade nätverk. De virtuella noderna har kon figurer ATS för att använda sina egna Azure Virtual Network-undernät. Det här under nätet har delegerade behörigheter för att ansluta Azure-resurser mellan AKS-klustret. Om du inte redan har ett delegerat undernät skapar och konfigurerar Azure Portal det virtuella Azure-nätverket och under nätet för användning med de virtuella noderna.

Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

Det tar några minuter för AKS-klustret att skapas och bli redo för användning.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Hantera Kubernetes-kluster med [kubectl][kubectl], Kubernetes kommandoradsklient. `kubectl`-klienten är förinstallerad i Azure Cloud Shell.

Om du vill öppna Cloud Shell väljer du **testa den** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Använd kommandot [AZ AKS get-credentials][az-aks-get-credentials] för att konfigurera `kubectl` att ansluta till ditt Kubernetes-kluster. I följande exempel hämtas autentiseringsuppgifterna för klusternamnet *myAKSCluster* i den resursgrupp som heter *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempel på utdata visar att den enskilda VM-noden har skapats och sedan den virtuella noden för Linux, *virtuell-Node-ACI-Linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempel App

I Azure Cloud Shell skapar du en fil med namnet `virtual-node.yaml` och kopierar i följande YAML. Om du vill schemalägga behållaren på noden definieras [en][node-selector] avsökning och [tolererande][toleration] . Med de här inställningarna kan Pod schemaläggas på den virtuella noden och bekräfta att funktionen har Aktiver ATS.

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

Kör programmet med kommandot [kubectl Apply][kubectl-apply] .

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Använd kommandot [kubectl get poddar][kubectl-get] med argumentet `-o wide` för att mata ut en lista över poddar och den schemalagda noden. Observera att `virtual-node-helloworld` Pod har schemalagts på noden `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod tilldelas en intern IP-adress från Azure Virtual Network-undernätet delegerad för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras i Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet][acr-aks-secrets]. En aktuell begränsning av virtuella noder är att du inte kan använda integrerad autentisering för Azure AD-tjänstens huvud namn. Om du inte använder en hemlighet kan poddar som schemalagts på virtuella noder inte starta och rapportera fel `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella noden Pod

Om du vill testa Pod som körs på den virtuella noden bläddrar du till demonstrations programmet med en webb klient. När Pod tilldelas en intern IP-adress kan du snabbt testa den här anslutningen från en annan Pod i AKS-klustret. Skapa en test-Pod och koppla en terminalsession till den:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Installera `curl` i pod med `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Nu kan du komma åt adressen till din POD med hjälp av `curl`, till exempel *http://10.241.0.4* . Ange din egna interna IP-adress som visas i föregående `kubectl get pods` kommando:

```azurecli-interactive
curl -L http://10.241.0.4
```

Demo programmet visas, som du ser i följande komprimerade exempel i utdata:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalfönstret för test Pod med `exit`. När sessionen är slut tas Pod bort.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har en POD schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänst distribution och dirigera trafik till din POD via en belastningsutjämnare eller ingångs kontroll. Mer information finns i [skapa en grundläggande ingress-styrenhet i AKS][aks-basic-ingress].

Virtuella noder är en komponent i en skalnings lösning i AKS. Mer information om skalnings lösningar finns i följande artiklar:

- [Använd Kubernetes horisontell Pod autoskalning][aks-hpa]
- [Använda Kubernetes-kluster autoskalning][aks-cluster-autoscaler]
- [Kolla in autoskalning-exemplet för virtuella noder][virtual-node-autoscale]
- [Läs mer om det virtuella Kubelet-biblioteket med öppen källkod][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
