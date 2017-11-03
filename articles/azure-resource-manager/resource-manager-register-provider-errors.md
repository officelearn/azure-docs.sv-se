---
title: Registrering av Azure-resurs Providerfel | Microsoft Docs
description: "Beskriver hur du löser registreringsfel i Azure-resurs-providern."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: d6a99917e732a3439a31cafa5608348694014054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel för registrering av resursprovider

Den här artikeln beskriver de fel som kan uppstå när du använder en resursleverantör som du tidigare inte har använt i din prenumeration.

## <a name="symptom"></a>Symtom

När du distribuerar resurs visas följande felkod och meddelande:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Eller, du kan få en liknande meddelande om att:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

## <a name="cause"></a>Orsak

Felen visas i någon av tre orsaker:

1. Resursprovidern har inte registrerats för din prenumeration
1. API-versionen stöds inte för resurstypen
1. Plats stöds inte för resurstypen

## <a name="solution"></a>Lösning

Felmeddelandet bör du få förslag på platser som stöds och API-versioner. Du kan ändra mallen till en av de föreslagna värdena. De flesta providers är registrerade automatiskt av Azure-portalen eller kommandoradsgränssnittet som du använder, men inte alla. Om du inte har använt en viss resurs-providern före kanske du behöver registrera providern. Du kan identifiera information om resursproviders via PowerShell eller Azure CLI.

### <a name="solution-1"></a>Lösning 1

PowerShell, Använd **Get-AzureRmResourceProvider** att se registreringsstatus för din.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Om du vill registrera en provider, Använd **registrera AzureRmResourceProvider** och ange namnet på resursprovidern som du vill registrera.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

För att få platser som stöds för en viss typ av resurs, använder du:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Om du vill hämta API-versioner som stöds för en viss typ av resurs, använder du:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

### <a name="solution-2"></a>Lösning 2

**Azure CLI**

Om providern är registrerad, Använd den `az provider list` kommando.

```azurecli-interactive
az provider list
```

Om du vill registrera en resursleverantör, använder den `az provider register` kommando och ange den *namnområde* att registrera.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Om du vill se vilka platser som stöds och API-versioner för en resurstyp, använder du:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

### <a name="solution-3"></a>Lösning 3

Du kan se registreringsstatus för och registrera en resursleverantörens namnrymd via portalen.

1. Din prenumeration, Välj **resursproviders**.

   ![Välj resursprovidrar](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Titta på listan över resursproviders och välj eventuellt den **registrera** länk till registerresursleverantören av den typ som du vill distribuera.

   ![Visa en lista över resursprovidrar](./media/resource-manager-register-provider-errors/list-resource-providers.png)
