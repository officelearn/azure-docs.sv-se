---
title: Kluster konfiguration i Azure Kubernetes Services (AKS)
description: Lär dig hur du konfigurerar ett kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725154"
---
# <a name="configure-an-aks-cluster"></a>Konfigurera ett AKS-kluster

Som en del av att skapa ett AKS-kluster kan du behöva anpassa kluster konfigurationen så att den passar dina behov. I den här artikeln beskrivs några alternativ för att anpassa ditt AKS-kluster.

## <a name="os-configuration-preview"></a>OS-konfiguration (för hands version)

AKS stöder nu Ubuntu 18,04 som Node Opera ting system (OS) i för hands versionen. Under för hands versions perioden är både Ubuntu 16,04 och Ubuntu 18,04 tillgängliga.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.2.0 eller senare
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

### <a name="new-clusters"></a>Nya kluster

Konfigurera klustret så att det använder Ubuntu 18,04 när klustret skapas. Använd `--aks-custom-headers` flaggan för att ställa in Ubuntu 18,04 som standard operativ system.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Om du vill skapa ett vanligt Ubuntu 16,04-kluster kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.

### <a name="existing-clusters"></a>Befintliga kluster

Konfigurera en ny Node-pool att använda Ubuntu 18,04. Använd `--aks-custom-headers` flaggan för att ställa in Ubuntu 18,04 som standard operativ system för den Node-poolen.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Om du vill skapa en vanlig Ubuntu 16,04-nod kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen.


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
- I listan med [vanliga frågor och svar om AKS](faq.md) hittar du svar på några vanliga frågor om AKS.