---
title: Använda hanterade identiteter i Azure Kubernetes Service
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 907aa83bc293aacd9920d8fd79a1b3184dd1d5dc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767591"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes Service

För närvarande kräver ett AKS-kluster (Azure Kubernetes Service) (särskilt Kubernetes molnprovider) en identitet för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure, den här identiteten kan vara antingen en *hanterad identitet* eller ett *tjänsthuvudnamn*. Om du använder ett [huvudnamn](kubernetes-service-principal.md)för tjänsten måste du antingen ange ett eller så måste AKS skapa ett för din räkning. Om du använder hanterad identitet skapas detta automatiskt av AKS. Kluster som använder tjänsthuvudnamn når så småningom ett tillstånd där tjänstens huvudnamn måste förnyas för att hålla klustret i arbete. Hantera tjänsthuvudnamn lägger till komplexitet, vilket är anledningen till att det är lättare att använda hanterade identiteter istället. Samma behörighetskrav gäller för både tjänsthuvudnamn och hanterade identiteter.

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
