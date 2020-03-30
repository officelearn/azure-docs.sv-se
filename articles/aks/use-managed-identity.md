---
title: Använda hanterade identiteter i Azure Kubernetes Service
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369975"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes Service

För närvarande kräver ett AKS-kluster (Azure Kubernetes Service) (särskilt Kubernetes molnleverantör) ett *tjänsthuvudnamn* för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure. Antingen måste du ange ett huvudnamn för tjänsten eller så skapar AKS ett för din räkning. Tjänstens huvudnamn har vanligtvis ett utgångsdatum. Kluster når så småningom ett tillstånd där tjänstens huvudnamn måste förnyas för att hålla klustret igång. Hantera tjänstens huvudnamn ökar komplexiteten.

*Hanterade identiteter* är i huvudsak ett omslag runt tjänstens huvudnamn och gör deras hantering enklare. Läs mer om [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS skapar två hanterade identiteter:

- **Systemtilldelade hanterade identitet:** Den identitet som Kubernetes-molnleverantören använder för att skapa Azure-resurser för användarens räkning. Livscykeln för den systemtilldelade identiteten är knuten till klustrets. Identiteten tas bort när klustret tas bort.
- **Användartilldelade hanterade identitet:** Den identitet som används för auktorisering i klustret. Den användartilldelade identiteten används till exempel för att auktorisera AKS att använda Azure Container Register (ACRs) eller för att auktorisera kubelet för att hämta metadata från Azure.

Tillägg autentiserar också med hjälp av en hanterad identitet. För varje tillägg skapas en hanterad identitet av AKS och varar under tilläggets livstid. Om du vill skapa och använda ditt eget virtuella nätverk, statisk IP-adress eller ansluten Azure-disk där resurserna ligger utanför resursgruppen MC_*använder du principalID i klustret för att utföra en rolltilldelning. Mer information om rolltilldelning finns i [Delegera åtkomst till andra Azure-resurser](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

- Azure CLI, version 2.2.0 eller senare

## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Du kan nu skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommandon.

Skapa först en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

En lyckad klusterskapande med hanterade identiteter innehåller den här tjänstens huvudprofilinformation:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Slutligen, få autentiseringsuppgifter för att komma åt klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klustret skapas om några minuter. Du kan sedan distribuera dina programarbetsbelastningar till det nya klustret och interagera med det precis som du har gjort med tjänsthuvudnamnsbaserade AKS-kluster.

> [!IMPORTANT]
>
> - AKS-kluster med hanterade identiteter kan endast aktiveras när klustret skapas.
> - Befintliga AKS-kluster kan inte uppdateras eller uppgraderas för att aktivera hanterade identiteter.
