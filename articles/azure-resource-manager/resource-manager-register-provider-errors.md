---
title: Registrerings fel för resurs leverantör
description: Beskriver hur du löser registrerings fel i Azure Resource Provider när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: 96595bab9d0db189911cac4fc1b42c722c2c1515
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150508"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel för registreringen av resursprovidern

Den här artikeln beskriver de fel som kan uppstå när du använder en provider för nätverksresurser som du inte tidigare har använt i din prenumeration.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

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

Eller, när du inaktiverar automatisk avstängning för virtuella datorer, kan du få ett fel meddelande som liknar:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Orsak

Du får de här felen av någon av följande orsaker:

* Den obligatoriska resurs leverantören har inte registrerats för din prenumeration
* API-versionen stöds inte för resurstypen
* Plats stöds inte för resurstypen
* För automatisk avstängning av virtuella datorer måste Microsoft. DevTestLab-resurs leverantören vara registrerad.

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

För PowerShell använder du **Get-AzResourceProvider** för att se din registrerings status.

```powershell
Get-AzResourceProvider -ListAvailable
```

Registrera en provider genom att använda **register-AzResourceProvider** och ange namnet på den resurs leverantör som du vill registrera.

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
