---
title: Klusterkonfiguration i Azure Kubernetes Services (AKS)
description: Lär dig hur du konfigurerar ett kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479168"
---
# <a name="configure-an-aks-cluster"></a>Konfigurera ett AKS-kluster

Som en del av att skapa ett AKS-kluster kan du behöva anpassa klusterkonfigurationen så att den passar dina behov. I den här artikeln finns några alternativ för att anpassa AKS-klustret.

## <a name="os-configuration-preview"></a>Os-konfiguration (förhandsversion)

AKS stöder nu Ubuntu 18.04 som nodoperativsystem (OS) i förhandsversion. Under förhandsgranskningsperioden är både Ubuntu 16.04 och Ubuntu 18.04 tillgängliga.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.2.0 eller senare
- Tillägget aks-preview 0.4.35

Om du vill installera tillägget aks-preview 0.4.35 eller senare använder du följande Azure CLI-kommandon:

```azurecli
az extension add --name aks-preview
az extension list
```

Registrera `UseCustomizedUbuntuPreview` funktionen:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrollera registreringsstatusen med kommandot [az-funktionslista:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar `Microsoft.ContainerService` du registreringen av resursprovidern med kommandot [az provider register:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Konfigurera klustret så att Ubuntu 18.04 används när klustret skapas. Använd `--aks-custom-headers` flaggan för att ange Ubuntu 18.04 som standard-OS.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Om du vill skapa ett vanligt Ubuntu 16.04-kluster kan du `--aks-custom-headers` göra det genom att utelämna den anpassade taggen.

## <a name="custom-resource-group-name"></a>Namn på anpassad resursgrupp

När du distribuerar ett Azure Kubernetes Service-kluster i Azure skapas en andra resursgrupp för arbetarnoderna. Som standard namnger AKS nodresursgruppen `MC_resourcegroupname_clustername_location`, men du kan också ange ditt eget namn.

Om du vill ange ditt eget resursgruppsnamn installerar du azure CLI-tilläggsversionen 0.3.2 eller senare. Använd Azure CLI och `--node-resource-group` använd `az aks create` parametern för kommandot för att ange ett anpassat namn för resursgruppen. Om du använder en Azure Resource Manager-mall för att distribuera ett AKS-kluster kan du definiera resursgruppsnamnet med hjälp av egenskapen. `nodeResourceGroup`

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Den sekundära resursgruppen skapas automatiskt av Azure-resursleverantören i din egen prenumeration. Observera att du bara kan ange det anpassade resursgruppsnamnet när klustret skapas. 

När du arbetar med nodresursgruppen bör du tänka på att du inte kan:

- Ange en befintlig resursgrupp för nodresursgruppen.
- Ange en annan prenumeration för nodresursgruppen.
- Ändra nodresursgruppnamnet när klustret har skapats.
- Ange namn för hanterade resurser inom nodresursgruppen.
- Ändra eller ta bort Azure-skapade taggar för hanterade resurser i nodresursgruppen.

## <a name="next-steps"></a>Nästa steg

- Lär dig `Kured` hur du använder för att [tillämpa säkerhets- och kärnuppdateringar på Linux-noder](node-updates-kured.md) i klustret.
- Se [Uppgradera ett AKS-kluster (Azure Kubernetes Service)](upgrade-cluster.md) om du vill lära dig hur du uppgraderar klustret till den senaste versionen av Kubernetes.
- Se listan med [vanliga frågor om AKS](faq.md) för att hitta svar på några vanliga AKS-frågor.