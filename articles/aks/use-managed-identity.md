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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827550"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>För hands version – Använd hanterade identiteter i Azure Kubernetes-tjänsten

För närvarande måste användare ange ett huvud namn för tjänsten eller en AKS som skapar en för din räkning i ordning för AKS-klustret (särskilt Kubernetes moln leverantör) för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure. Tjänstens huvud namn skapas vanligt vis med ett förfallo datum. Kluster kommer slutligen att ha ett tillstånd där tjänstens huvud namn måste förnyas annars fungerar inte klustret. Hantering av tjänstens huvud namn ökar komplexiteten. Hanterade identiteter är i grunden en omslutning av tjänstens huvud namn och gör hanteringen enklare. Läs mer om den [hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) artikeln.

AKS skapar två hanterade identiteter ett system som tilldelats en hanterad identitet och den andra användaren tilldelas identitet. En systemtilldelad hanterad identitet används av Kubernetes Cloud Provider för att skapa Azure-resurser för användarens räkning. Livs cykeln för den här systemtilldelade hanterade identiteten är kopplad till klustrets och tas bort när klustret tas bort. AKS skapar också en användare som tilldelats en hanterad identitet som används i klustret för att auktorisera AKS för att få åtkomst till ACR: er, kubelet för att hämta metadata från Azure osv.

I den här för hands perioden krävs ett huvud namn för tjänsten fortfarande. Det kommer att användas för auktorisering av tillägg som övervakning, virtuell nod, Azure-princip och http-programroutning. Det finns arbete som pågår för att ta bort beroendet av tilläggen på SPN och så småningom SPN-krav i AKS kommer att tas bort helt.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS](support-policies.md)
> * [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande:

* Du behöver också Azure CLI-version 2.0.70 eller senare och AKS 0.4.14-tillägget för-Preview

## <a name="install-latest-aks-cli-preview-extension"></a>Installera det senaste AKS CLI Preview-tillägget

Du behöver tillägget **AKS-Preview 0.4.14** eller senare.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av [AZ feature list] [AZ-feature-list] kommandot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

När ett tillstånd har registrerats uppdaterar du registreringen av *Microsoft. container service* -resurs leverantören med hjälp av [AZ Provider register] [AZ-Provider-registrera] kommandot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Skapa ett AKS-kluster med hanterad identitet

Nu kan du skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommando
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Hämta autentiseringsuppgifter för åtkomst till klustret
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
När klustret har skapats på några minuter kan du distribuera dina program arbets belastningar och interagera med det som du har med tjänstens huvud namn baserade AKS-kluster. 

> [!IMPORTANT]
> * AKS-kluster med hanterade identiteter kan bara aktive ras när klustret skapas
> * Det går inte att uppdatera/uppgradera befintliga AKS-kluster för att aktivera hanterade identiteter