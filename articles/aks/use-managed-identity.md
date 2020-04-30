---
title: Använda hanterade identiteter i Azure Kubernetes-tjänsten
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112974"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes-tjänsten

För närvarande kräver ett Azure Kubernetes service (AKS)-kluster (specifikt Kubernetes Cloud Provider) en identitet för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure, den här identiteten kan antingen vara en *hanterad identitet* eller ett *huvud namn för tjänsten*. Om du använder ett [huvud namn för tjänsten](kubernetes-service-principal.md)måste du antingen ange ett eller AKS skapar ett för din räkning. Om du använder hanterad identitet skapas detta automatiskt åt dig av AKS. Kluster som använder tjänstens huvud namn når till sist ett tillstånd där tjänstens huvud namn måste förnyas för att hålla klustret igång. Hantering av tjänstens huvud namn ökar komplexiteten, vilket innebär att det är lättare att använda hanterade identiteter i stället. Samma behörighets krav gäller för både tjänstens huvud namn och hanterade identiteter.

*Hanterade identiteter* är i grunden en omslutning av tjänstens huvud namn och gör hanteringen enklare. Läs mer om [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS skapar två hanterade identiteter:

- **Systemtilldelad hanterad identitet**: den identitet som används av Kubernetes Cloud Provider för att skapa Azure-resurser för användarens räkning. Livs cykeln för den systemtilldelade identiteten är kopplad till klustret. Identiteten tas bort när klustret tas bort.
- **Användarens tilldelade hanterade identitet**: den identitet som används för auktorisering i klustret. Den användardefinierade identiteten används till exempel för att auktorisera AKS till att använda Azure Container register (ACR: er), eller för att auktorisera kubelet för att hämta metadata från Azure.

Tillägg autentiseras också med hjälp av en hanterad identitet. För varje tillägg skapas en hanterad identitet av AKS och varar för livs längden för tillägget. 

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

- Azure CLI, version 2.2.0 eller senare

## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Nu kan du skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommandon.

Börja med att skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Ett lyckat kluster skapas med hanterade identiteter som innehåller den här tjänstens huvud princips profil information:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> För att skapa och använda ditt eget VNet, en statisk IP-adress eller en ansluten Azure-disk där resurserna ligger utanför MC_ *-resurs gruppen, använder du PrincipalID för den tilldelade hanterade identiteten i kluster systemet för att utföra en roll tilldelning. Mer information om roll tilldelning finns i [Delegera åtkomst till andra Azure-resurser](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Behörighets bidrag till kluster hanterad identitet som används av Azure Cloud Provider kan ta upp till 60 minuter att fylla i.

Slutligen kan du hämta autentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klustret kommer att skapas på några minuter. Du kan sedan distribuera dina program arbets belastningar till det nya klustret och interagera med det precis som du har gjort med service-huvudbaserade AKS-kluster.

> [!IMPORTANT]
>
> - AKS-kluster med hanterade identiteter kan bara aktive ras när klustret skapas.
> - Det går inte att uppdatera eller uppgradera befintliga AKS-kluster för att aktivera hanterade identiteter.
