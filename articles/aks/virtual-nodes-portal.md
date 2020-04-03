---
title: Skapa virtuella noder med hjälp av portalen i Azure Kubernetes Services (AKS)
description: Lär dig hur du använder Azure-portalen för att skapa ett AKS-kluster (Azure Kubernetes Services) som använder virtuella noder för att köra poddar.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 696821e12e963292107cad5b22f00a9816a94b25
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616414"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Skapa och konfigurera ett AKS-kluster (Azure Kubernetes Services) för att använda virtuella noder i Azure-portalen

Om du snabbt vill distribuera arbetsbelastningar i ett AKS-kluster (Azure Kubernetes Service) kan du använda virtuella noder. Med virtuella noder har du snabb etablering av poddar och betalar bara per sekund för deras körningstid. I ett skalningsscenario behöver du inte vänta på att Kubernetes-klustrets automatiskskalerare distribuerar VM-beräkningsnoder för att köra ytterligare poddar. Virtuella noder stöds endast med Linux-poddar och noder.

Den här artikeln visar hur du skapar och konfigurerar virtuella nätverksresurser och ett AKS-kluster med virtuella noder aktiverade.

## <a name="before-you-begin"></a>Innan du börjar

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett virtuellt nätverksundernät och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med hjälp av *avancerade* nätverk. Som standard skapas AKS-kluster med *grundläggande* nätverk. Den här artikeln visar hur du skapar ett virtuellt nätverk och undernät och distribuerar sedan ett AKS-kluster som använder avancerade nätverk.

Om du inte tidigare har använt ACI registrerar du tjänsteleverantören med din prenumeration. Du kan kontrollera status för ACI-providerregistreringen med kommandot [az provider list,][az-provider-list] som visas i följande exempel:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance-providern* ska rapportera som *registrerad*, vilket visas i följande exempelutdata:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Om leverantören visar som *NotRegistered*registrerar du leverantören med hjälp av [det az-leverantörsregistret][az-provider-register] som visas i följande exempel:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regional tillgänglighet

Följande regioner stöds för virtuella noddistributioner:

* Östra Australien (australieniska)
* Centrala USA (centralus)
* Östlig US (eastus)
* Östra US 2 (eastus2)
* Östra Japan (japaneast)
* Norra Europa (northeurope)
* Sydostasien (sydostliga)
* Västra central-US (westcentralus)
* Västeuropa (westeurope)
* Västra US (westus)
* Västra US 2 (westus2)

## <a name="known-limitations"></a>Kända begränsningar
Funktionen Virtuella noder är starkt beroende av ACI:s funktionsuppsättning. Följande scenarier stöds ännu inte med virtuella noder

* Använda tjänstens huvudnamn för att hämta ACR-bilder. [Lösning](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) är att använda [Kubernetes hemligheter](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Begränsningar för virtuella nätverk,](../container-instances/container-instances-vnet.md) inklusive VNet-peering, Kubernetes-nätverksprinciper och utgående trafik till internet med nätverkssäkerhetsgrupper.
* Init behållare
* [Värdalias](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argument](../container-instances/container-instances-exec.md#restrictions) för exec i ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) distribuerar inte poddar till den virtuella noden
* [Windows Server-noder (för närvarande i förhandsversion i AKS)](windows-container-cli.md) stöds inte tillsammans med virtuella noder. Du kan använda virtuella noder för att schemalägga Windows Server-behållare utan att windows servernoder behöver användas i ett AKS-kluster.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

I det övre vänstra hörnet av Azure-portalen väljer du **Skapa en resurs** > **Kubernetes Service**.

Konfigurera följande alternativ på sidan **Grunderna:**

- *PROJEKTINFORMATION*: Välj en Azure-prenumeration och välj sedan eller skapa en Azure-resursgrupp, till exempel *myResourceGroup*. Ange ett **Kubernetes-klusternamn**, till exempel *myAKSCluster*.
- *KLUSTERINFORMATION*: Välj en region, en Kubernetes-version och ett DNS-namnprefix för AKS-klustret.
- *PRIMÄR NOD POOL:* Välj en VM-storlek för AKS-noderna. VM-storleken **kan inte** ändras efter att ett AKS-kluster har distribuerats.
     - Välj även det antal noder som ska distribueras till klustret. För den här artikeln anger du **antalet noder** till *1*. Antalet noder **kan** justeras efter att klustret har distribuerats.

Klicka på **Nästa: Skala**.

På sidan **Skala** väljer du *Aktiverad* under **Virtuella noder**.

![Skapa AKS-kluster och aktivera de virtuella noderna](media/virtual-nodes-portal/enable-virtual-nodes.png)

Som standard skapas ett huvudnamn för Tjänsten Azure Active Directory. Den här tjänstens huvudnamn används för klusterkommunikation och integrering med andra Azure-tjänster.

Klustret är också konfigurerat för avancerade nätverk. De virtuella noderna är konfigurerade för att använda sitt eget Azure-virtuella nätverksundernät. Det här undernätet har delegerade behörigheter för att ansluta Azure-resurser mellan AKS-klustret. Om du inte redan har delegerat undernät skapar och konfigurerar Azure-portalen det virtuella Nätverket och undernätet för användning med de virtuella noderna.

Välj **Granska + skapa**. När valideringen är klar väljer du **Skapa**.

Det tar några minuter för AKS-klustret att skapas och bli redo för användning.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Hantera Kubernetes-kluster med [kubectl][kubectl], Kubernetes kommandoradsklient. `kubectl`-klienten är förinstallerad i Azure Cloud Shell.

Om du vill öppna Cloud Shell väljer du **Prova det** i det övre högra hörnet av ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/bash](https://shell.azure.com/bash)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Använd kommandot [az aks get-credentials][az-aks-get-credentials] för att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster. I följande exempel hämtas autentiseringsuppgifterna för klusternamnet *myAKSCluster* i den resursgrupp som heter *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempelutdata visar den enda VM-noden som skapats och sedan den virtuella noden för Linux, *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuera en exempelapp

Skapa en fil som heter `virtual-node.yaml` och kopiera i följande YAML i Azure Cloud Shell. Om du vill schemalägga behållaren på noden definieras en [nodSelector][node-selector] och [toleration.][toleration] Med de här inställningarna kan podden schemaläggas på den virtuella noden och bekräfta att funktionen är aktiverad.

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

Kör programmet med kommandot [kubectl apply.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Använd kommandot [kubectl get pods][kubectl-get] med `-o wide` argumentet att mata ut en lista över poddar och den schemalagda noden. Observera att `virtual-node-helloworld` podden har `virtual-node-linux` schemalagts på noden.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Podden tilldelas en intern IP-adress från Azures virtuella nätverksundernät som delegerats för användning med virtuella noder.

> [!NOTE]
> Om du använder avbildningar som lagras i Azure Container Registry [konfigurerar och använder du en Kubernetes-hemlighet][acr-aks-secrets]. En aktuell begränsning av virtuella noder är att du inte kan använda integrerad Azure AD-tjänsthuvudtillståndsautentisering. Om du inte använder en hemlighet kan poddar som schemalagts `HTTP response status code 400 error code "InaccessibleImage"`på virtuella noder inte starta och rapportera felet .

## <a name="test-the-virtual-node-pod"></a>Testa den virtuella nodpodden

Om du vill testa podden som körs på den virtuella noden bläddrar du till demoprogrammet med en webbklient. Eftersom podden tilldelas en intern IP-adress kan du snabbt testa den här anslutningen från en annan pod i AKS-klustret. Skapa en testkapsel och bifoga en terminalsession till den:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Installera `curl` i pod `apt-get`med:

```console
apt-get update && apt-get install -y curl
```

Nu tillgång till adressen `curl`till din *http://10.241.0.4*pod med , till exempel . Ange din egen interna IP-adress som visas i föregående `kubectl get pods` kommando:

```console
curl -L http://10.241.0.4
```

Demoprogrammet visas, som visas i följande komprimerade exempelutdata:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Stäng terminalsessionen till testenheten med `exit`. När sessionen är avslutad är podden den borttagna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har en pod schemalagts på den virtuella noden och tilldelats en privat, intern IP-adress. Du kan i stället skapa en tjänstdistribution och dirigera trafik till din pod via en belastningsutjämnare eller ingående styrenhet. Mer information finns [i Skapa en grundläggande ingress-styrenhet i AKS][aks-basic-ingress].

Virtuella noder är en komponent i en skalningslösning i AKS. Mer information om skalningslösningar finns i följande artiklar:

- [Använda kubernetes horisontella pod autoscaler][aks-hpa]
- [Använda Kubernetes-kluster automatisk skalning][aks-cluster-autoscaler]
- [Kolla in exemplet för automatisk skalning för virtuella noder][virtual-node-autoscale]
- [Läs mer om Virtual Kubelet open source-bibliotek][virtual-kubelet-repo]

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