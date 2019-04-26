---
title: Azure resource provider registreringsfel | Microsoft Docs
description: Beskriver hur du löser registreringsfel för Azure resource provider.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2f3db5e6260b065c83f0e337306d38dca6e5ff51
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389955"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel för registreringen av resursprovidern

Den här artikeln beskriver de fel som kan uppstå när du använder en provider för nätverksresurser som du inte tidigare har använt i din prenumeration.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symtom

När du distribuerar resurs visas följande felkod och meddelandet:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Eller så visas ett meddelande liknande som säger:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Felmeddelandet bör ge dig förslag på platser som stöds och API-versioner. Du kan ändra mallen till något av de föreslagna värdena. De flesta leverantörer är registrerade automatiskt av Azure portal eller kommandoradsgränssnittet som du använder, men inte alla. Om du inte har använt en viss resursprovider innan du kan behöva du registrera den providern.

Eller när du inaktiverar automatisk avstängning för virtuella datorer, så visas ett felmeddelande som liknar:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Orsak

De här felen visas i något av följande skäl:

* Den nödvändiga resursprovidern har inte registrerats för din prenumeration
* API-versionen stöds inte för resurstypen
* Plats stöds inte för resurstypen
* Resursprovidern Microsoft.DevTestLab för automatisk avstängning av virtuella datorer, måste vara registrerad.

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

Du använder PowerShell använder **Get-AzResourceProvider** att se registreringsstatus för din.

```powershell
Get-AzResourceProvider -ListAvailable
```

Registrera en provider **registrera AzResourceProvider** och ange namnet på resursprovidern som du vill registrera.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Hämta platserna som stöds för en viss typ av resurs med:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Hämta API-versioner som stöds för en viss typ av resurs med:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Lösning 2 – Azure CLI

Om du vill se om providern är registrerad, använda den `az provider list` kommando.

```azurecli-interactive
az provider list
```

Registrera en resursprovider genom att använda den `az provider register` kommandot och ange den *namnområde* att registrera.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Om du vill se de platser som stöds och API-versioner för en resurstyp, använder du:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Lösning 3 – Azure-portalen

Du kan se registreringsstatus och registrerar en resursproviderns namnområde via portalen.

1. Från portalen, väljer **alla tjänster**.

   ![Välj alla tjänster](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Välj **Prenumerationer**.

   ![Välj prenumerationer](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Välj den prenumeration som du vill använda för att registrera resursprovidern i listan över prenumerationer.

   ![Välj prenumeration för att registrera resursprovidern](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Din prenumeration, Välj **resursprovidrar**.

   ![Välj resursprovidrar](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Titta på listan över resursprovidrar och om det behövs väljer den **registrera** länk för att registrera resursprovidern för den typ som du försöker distribuera.

   ![Lista resursprovidrar](./media/resource-manager-register-provider-errors/list-resource-providers.png)
