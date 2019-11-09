---
title: Använda hanterade identiteter i Azure Kubernetes-tjänsten
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes service (AKS)
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 16a1f3b8f9ef036bcdc9af122243d313186f99f1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885341"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>För hands version – Använd hanterade identiteter i Azure Kubernetes-tjänsten

För närvarande kräver ett Azure Kubernetes service (AKS)-kluster (specifikt Kubernetes Cloud Provider) ett *huvud namn för tjänsten* för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure. Antingen måste du ange ett huvud namn för tjänsten eller AKS skapar ett för din räkning. Tjänstens huvud namn har vanligt vis ett förfallo datum. Kluster når slutligen ett tillstånd där tjänstens huvud namn måste förnyas för att hålla klustret igång. Hantering av tjänstens huvud namn ökar komplexiteten.

*Hanterade identiteter* är i grunden en omslutning av tjänstens huvud namn och gör hanteringen enklare. Läs mer om [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS skapar två hanterade identiteter:

- **Systemtilldelad hanterad identitet**: den identitet som används av Kubernetes Cloud Provider för att skapa Azure-resurser för användarens räkning. Livs cykeln för den systemtilldelade identiteten är kopplad till klustret. Identiteten tas bort när klustret tas bort.
- **Användarens tilldelade hanterade identitet**: den identitet som används för auktorisering i klustret. Den användardefinierade identiteten används till exempel för att auktorisera AKS till att använda Access Control-poster (ACR: er) eller för att auktorisera kubelet för att hämta metadata från Azure.

I den här för hands perioden krävs ett huvud namn för tjänsten fortfarande. Den används för auktorisering av tillägg som övervakning, virtuella noder, Azure Policy och HTTP-programroutning. Arbetet pågår för att ta bort beroendet av tillägg på tjänstens huvud namn (SPN). Slutligen kommer kravet på ett SPN i AKS att tas bort helt.

> [!IMPORTANT]
> AKS för hands versions funktioner är tillgängliga på en självbetjänings-och deltagande nivå. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och omfattas inte av service nivå avtal och begränsad garanti. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Mer information finns i följande support artiklar:
>
> - [Support principer för AKS](support-policies.md)
> - [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurser installerade:

- Azure CLI, version 2.0.70 eller senare
- Tillägget AKS-Preview 0.4.14

Använd följande Azure CLI-kommandon för att installera AKS-Preview 0.4.14-tillägget eller senare:

```azurecli
az extension update --name aks-preview
az extension list
```

> [!CAUTION]
> När du har registrerat en funktion på en prenumeration kan du för närvarande inte avregistrera den funktionen. När du aktiverar vissa för hands versions funktioner kan du använda standardvärden för alla AKS-kluster som skapas efteråt i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd i stället en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` Resource Provider med hjälp av [AZ Provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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

Slutligen kan du hämta autentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klustret kommer att skapas på några minuter. Du kan sedan distribuera dina program arbets belastningar till det nya klustret och interagera med det precis som du har gjort med service-huvudbaserade AKS-kluster.

> [!IMPORTANT]
>
> - AKS-kluster med hanterade identiteter kan bara aktive ras när klustret skapas.
> - Det går inte att uppdatera eller uppgradera befintliga AKS-kluster för att aktivera hanterade identiteter.
