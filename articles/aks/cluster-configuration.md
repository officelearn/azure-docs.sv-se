---
title: Kluster konfiguration i Azure Kubernetes Services (AKS)
description: Lär dig hur du konfigurerar ett kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3c8d374935c777548d1dc0d43ccd131fe21fd509
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856103"
---
# <a name="configure-an-aks-cluster"></a>Konfigurera ett AKS-kluster

Som en del av att skapa ett AKS-kluster kan du behöva anpassa kluster konfigurationen så att den passar dina behov. I den här artikeln beskrivs några alternativ för att anpassa ditt AKS-kluster.

## <a name="os-configuration-preview"></a>OS-konfiguration (för hands version)

AKS stöder nu Ubuntu 18,04 som Node Opera ting system (OS) i för hands versionen. Under för hands versions perioden är både Ubuntu 16,04 och Ubuntu 18,04 tillgängliga.

> [!IMPORTANT]
> Resurspooler som skapats på Kubernetes v-1.18 eller högre standardvärden till en obligatorisk `AKS Ubuntu 18.04` Node-avbildning. Nodkonfigurationer på en Kubernetes-version som stöds är mindre än 1,18 ta emot `AKS Ubuntu 16.04` som Node-avbildningen, men kommer att uppdateras till `AKS Ubuntu 18.04` en gång som Kubernetes-versionen för Node-poolen uppdateras till v 1.18 eller senare.
> 
> Vi rekommenderar starkt att du testar dina arbets belastningar på AKS Ubuntu 18,04-nodkonfigurationer innan du använder kluster på 1,18 eller senare. Läs om hur du [testar Ubuntu 18,04-nodkonfigurationer](#use-aks-ubuntu-1804-existing-clusters-preview).

Du måste ha följande resurser installerade:

- [Azure CLI][azure-cli-install], version 2.2.0 eller senare
- Tillägget AKS-Preview 0.4.35

Använd följande Azure CLI-kommandon för att installera AKS-Preview 0.4.35-tillägget eller senare:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrera `UseCustomizedUbuntuPreview` funktionen:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resurs leverantören med hjälp av [AZ Provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) kommando:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Använda AKS Ubuntu 18,04 på nya kluster (förhands granskning)

Konfigurera klustret så att det använder Ubuntu 18,04 när klustret skapas. Använd `--aks-custom-headers` flaggan för att ställa in Ubuntu 18,04 som standard operativ system.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Om du vill skapa kluster med AKS Ubuntu 16,04-avbildningen kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Använda AKS Ubuntu 18,04 befintliga kluster (förhands granskning)

Konfigurera en ny Node-pool att använda Ubuntu 18,04. Använd `--aks-custom-headers` flaggan för att ställa in Ubuntu 18,04 som standard operativ system för den Node-poolen.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Om du vill skapa Node-pooler med AKS Ubuntu 16,04-avbildningen kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.


## <a name="container-runtime-configuration-preview"></a>Konfiguration av container Runtime (för hands version)

En container runtime är program vara som kör behållare och hanterar behållar avbildningar på en nod. Körningen gör att du kan köra behållare på Linux eller Windows med hjälp av en viss funktion i abstrakta sys-anrop eller operativ system (OS). Idag AKS använder [Moby](https://mobyproject.org/) (överordnad Docker) som container Runtime. 
    
![Docker-CRI](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/)är ett [OCI](https://opencontainers.org/) -kompatibelt (Open container Initiative)-kompatibel Core container runtime som innehåller den minsta uppsättningen nödvändiga funktioner för att köra behållare och hantera avbildningar på en nod. Den har [donerat](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) till Cloud Native Compute Foundation (CNCF) i mars 2017. Den aktuella Moby-versionen som AKS använder redan idag utnyttjar och är byggd ovanpå `containerd` , som visas ovan. 

Med en behållar-baserad nod och nodkonfigurationer, i stället för att prata med `dockershim` , kommer kubelet att kommunicera direkt med `containerd` via CRI-plugin-programmet (container runtime Interface) och ta bort extra hopp i flödet jämfört med Docker-CRI-implementeringen. Därför ser du bättre Pod start latens och mindre resurs (CPU-och minnes användning).

Genom `containerd` att använda för AKS-noder förbättrar Pod-startsvars tiden och resursanvändningen för resurs användningen av container runtime minskar. Dessa förbättringar aktive ras i den här nya arkitekturen där kubelet `containerd` kommunicerar direkt med CRI-plugin-programmet medan Moby/Docker Architecture kubelet pratar med `dockershim` och Docker-motorn innan den når `containerd` , och därför har extra hopp i flödet.

![Docker-CRI](media/cluster-configuration/containerd-cri.png)

`Containerd`fungerar på alla GA-versioner av Kubernetes i AKS och i varje överordnad Kubernetes-version över v 1.10 och stöder alla Kubernetes-och AKS-funktioner.

> [!IMPORTANT]
> När `containerd` blir allmänt tillgänglig på AKS är det standard alternativet och endast tillgängligt för container runtime på nya kluster. Du kan fortfarande använda Moby-nodepools och-kluster på äldre versioner som stöds tills de faller utanför supporten. 
> 
> Vi rekommenderar att du testar dina arbets belastningar på `containerd` nodkonfigurationer innan du uppgraderar eller skapar nya kluster med denna container Runtime.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Använd `containerd` som behållar körning (för hands version)

Du måste ha följande för hands krav:

- [Azure CLI][azure-cli-install], version 2.8.0 eller senare installerat
- AKS – för hands versions tillägget 0.4.53 eller senare
- `UseCustomizedContainerRuntime`Funktions flaggan har registrerats
- `UseCustomizedUbuntuPreview`Funktions flaggan har registrerats

Använd följande Azure CLI-kommandon för att installera AKS-Preview 0.4.53-tillägget eller senare:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrera `UseCustomizedContainerRuntime` funktionerna och `UseCustomizedUbuntuPreview` :

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resurs leverantören med hjälp av [AZ Provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) kommando:

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Använd `containerd` på nya kluster (förhands granskning)

Konfigurera klustret som ska användas `containerd` när klustret skapas. Använd `--aks-custom-headers` flaggan för att ange `containerd` som container Runtime.

> [!NOTE]
> `containerd`Körnings miljön stöds bara på noder och nodkonfigurationer med hjälp av AKS Ubuntu 18,04-avbildningen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Om du vill skapa kluster med Moby-körningsmiljön (Docker) kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.

### <a name="use-containerd-on-existing-clusters-preview"></a>Använd `containerd` på befintliga kluster (förhands granskning)

Konfigurera en ny Node-pool som ska användas `containerd` . Använd `--aks-custom-headers` flaggan för att ställa in `containerd` som körning för den noden.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Om du vill skapa Node-pooler med Moby-körningsmiljön (Docker) kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.


### <a name="containerd-limitationsdifferences"></a>`Containerd`begränsningar/skillnader

* Om du vill använda `containerd` som container runtime måste du använda AKS Ubuntu 18,04 som bas operativ system avbildning.
* Även om Docker-verktygen fortfarande finns på noderna, används Kubernetes `containerd` som behållar körning. Eftersom Moby/Docker inte hanterar Kubernetes-skapade behållare på noderna kan du därför inte Visa eller interagera med dina behållare med hjälp av Docker-kommandon (t `docker ps` . ex.) eller Docker-API: et.
* För `containerd` rekommenderar vi att du använder [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) som en ERSÄTTNINGS-cli i stället för Docker CLI för **fel sökning** av poddar, behållare och behållar avbildningar på Kubernetes-noder (till exempel `crictl ps` ). 
   * Den ger inte den fullständiga funktionen i Docker CLI. Den är endast avsedd för fel sökning.
   * `crictl`ger en mer Kubernetes vy över behållare, med begrepp som poddar osv.
* `Containerd`ställer in loggning med hjälp av standardiserat `cri` loggnings format (vilket skiljer sig från vad du för närvarande får från Docker-JSON-drivrutinen). Din loggnings lösning måste ha stöd för `cri` loggnings formatet (t. ex. [Azure Monitor för behållare](../azure-monitor/insights/container-insights-enable-new-cluster.md))
* Du kan inte längre komma åt Docker-motorn `/var/run/docker.sock` eller använda Docker-in-Docker (DinD).
  * Om du för närvarande extraherar program loggar eller övervakar data från Docker-motorn, bör du använda något som [Azure Monitor för behållare](../azure-monitor/insights/container-insights-enable-new-cluster.md) i stället. Dessutom har AKS inte stöd för att köra out-of-band-kommandon på de agent-noder som kan orsaka instabilitet.
  * Även om du använder Moby/Docker kan det vara starkt att skapa avbildningar och direkt dra nytta av Docker-motorn via metoderna ovan. Kubernetes är inte helt medvetna om dessa förbrukade resurser och dessa metoder visar flera problem som beskrivs [här](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) och [här](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), till exempel.
* Skapa bilder – den rekommenderade metoden för att skapa bilder är att använda [ACR-uppgifter](../container-registry/container-registry-quickstart-task-cli.md). En annan metod är att använda säkrare kluster alternativ som [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines-preview"></a>Virtuella datorer i generation 2 (för hands version)

Azure stöder [virtuella datorer i generation 2 (Gen2)](../virtual-machines/windows/generation-2.md). Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1 (gen1). Dessa funktioner omfattar ökat minne, Intel Software Guard-tillägg (Intel SGX) och virtualiserat beständigt minne (vPMEM).

Virtuella datorer i generation 2 använder den nya UEFI-baserade start arkitekturen i stället för den BIOS-baserade arkitekturen som används av virtuella datorer i generation 1.
Endast vissa SKU: er och storlekar har stöd för virtuella Gen2-datorer. Kontrol lera [listan över storlekar som stöds](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)för att se om din SKU stöder eller kräver Gen2.

Alla VM-avbildningar stöder inte heller Gen2, på AKS Gen2-VM: ar kommer att använda den nya [AKS Ubuntu 18,04-avbildningen](#os-configuration-preview). Den här avbildningen stöder alla Gen2 SKU: er och storlekar.

Om du vill använda Gen2-datorer under för hands versionen behöver du:
- `aks-preview`CLI-tillägget installerat.
- `Gen2VMPreview`Funktions flaggan har registrerats.

Registrera `Gen2VMPreview` funktionen:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resurs leverantören med hjälp av [AZ Provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) kommando:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

För att installera AKS-Preview CLI-tillägget använder du följande Azure CLI-kommandon:

```azurecli
az extension add --name aks-preview
```

För att uppdatera AKS-Preview CLI-tillägget använder du följande Azure CLI-kommandon:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Använda virtuella Gen2-datorer på nya kluster (förhands granskning)
Konfigurera klustret för att använda Gen2 virtuella datorer för den valda SKU: n när klustret skapas. Använd `--aks-custom-headers` flaggan för att ange Gen2 som generationens virtuella datorer i ett nytt kluster.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Om du vill skapa ett vanligt kluster med virtuella datorer i generation 1 (gen1) kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen. Du kan också välja att lägga till fler gen1-eller Gen2-VM: ar enligt nedan.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Använda virtuella Gen2-datorer på befintliga kluster (förhands granskning)
Konfigurera en ny Node-pool så att den använder virtuella Gen2-datorer. Använd `--aks-custom-headers` flaggan för att ange Gen2 som generering av virtuella datorer för den noden.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Om du vill skapa gen1 för vanliga noder kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.

## <a name="custom-resource-group-name"></a>Namn på anpassad resurs grupp

När du distribuerar ett Azure Kubernetes service-kluster i Azure skapas en andra resurs grupp för arbetsnoderna. Som standard namnger AKS resurs gruppen för noden `MC_resourcegroupname_clustername_location` , men du kan också ange ett eget namn.

Om du vill ange ett eget namn på en resurs grupp installerar du AKS-Preview Azure CLI-tillägget version 0.3.2 eller senare. Använd- `--node-resource-group` parametern för `az aks create` kommandot för att ange ett eget namn för resurs gruppen med hjälp av Azure CLI. Om du använder en Azure Resource Manager-mall för att distribuera ett AKS-kluster kan du definiera resurs gruppens namn genom att använda `nodeResourceGroup` egenskapen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Den sekundära resurs gruppen skapas automatiskt av Azure Resource Provider i din egen prenumeration. Du kan bara ange namnet på den anpassade resurs gruppen när klustret skapas. 

När du arbetar med resurs gruppen för noden bör du tänka på att du inte kan:

- Ange en befintlig resurs grupp för resurs gruppen för noden.
- Ange en annan prenumeration för resurs gruppen för noden.
- Ändra resurs grupp namnet för noden när klustret har skapats.
- Ange namn för de hanterade resurserna i resurs gruppen för noden.
- Ändra eller ta bort Azure-skapade Taggar av hanterade resurser i resurs gruppen för noden.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder `Kured` för att [tillämpa säkerhets-och kernel-uppdateringar på Linux-noder](node-updates-kured.md) i klustret.
- Se [uppgradera ett Azure Kubernetes service (AKS)-kluster](upgrade-cluster.md) för att lära dig hur du uppgraderar klustret till den senaste versionen av Kubernetes.
- Läs mer om [ `containerd` och Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- I listan med [vanliga frågor och svar om AKS](faq.md) hittar du svar på några vanliga frågor om AKS.


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register