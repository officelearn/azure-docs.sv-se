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
ms.date: 12/07/2018
ms.author: tomfitz
ms.openlocfilehash: 787d6549eb8413c9dcfc0c906167cc36d4cff6b0
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135720"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel för registreringen av resursprovidern

Den här artikeln beskriver de fel som kan uppstå när du använder en provider för nätverksresurser som du inte tidigare har använt i din prenumeration.

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

## <a name="cause"></a>Orsak

De här felen visas i någon av tre orsaker:

1. Resursprovidern har inte registrerats för din prenumeration
1. API-versionen stöds inte för resurstypen
1. Plats stöds inte för resurstypen

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

Du använder PowerShell använder **Get-AzureRmResourceProvider** att se registreringsstatus för din.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Registrera en provider **Register-AzureRmResourceProvider** och ange namnet på resursprovidern som du vill registrera.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Hämta platserna som stöds för en viss typ av resurs med:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Hämta API-versioner som stöds för en viss typ av resurs med:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
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
