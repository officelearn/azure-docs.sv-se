---
title: Registreringsfel för resursprovider
description: Beskriver hur du löser registreringsfel för Azure-resursprovider när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273779"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel vid registreringen av resursprovidern

I den här artikeln beskrivs de fel som kan uppstå när du använder en resursleverantör som du inte tidigare har använt i prenumerationen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

När du distribuerar resurs kan följande felkod och meddelande visas:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Du kan också få ett liknande meddelande som anger:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Felmeddelandet bör ge dig förslag på platser och API-versioner som stöds. Du kan ändra mallen till ett av de föreslagna värdena. De flesta leverantörer registreras automatiskt av Azure-portalen eller kommandoradsgränssnittet som du använder, men inte alla. Om du inte har använt en viss resursleverantör tidigare kan du behöva registrera den leverantören.

Om du inaktiverar automatisk avstängning för virtuella datorer kan du också få ett felmeddelande som liknar:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Orsak

Du får dessa fel av något av följande skäl:

* Den resursleverantör som krävs har inte registrerats för din prenumeration
* API-version stöds inte för resurstypen
* Plats stöds inte för resurstypen
* För automatisk avstängning av virtuella datorer måste resursleverantören Microsoft.DevTestLab registreras.

## <a name="solution-1---powershell"></a>Lösning 1 - PowerShell

För PowerShell använder du **Get-AzResourceProvider** för att se din registreringsstatus.

```powershell
Get-AzResourceProvider -ListAvailable
```

Om du vill registrera en leverantör använder du **Register-AzResourceProvider** och anger namnet på den resursleverantör som du vill registrera.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Om du vill hämta de platser som stöds för en viss typ av resurs använder du:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Om du vill hämta API-versioner som stöds för en viss typ av resurs använder du:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Lösning 2 - Azure CLI

Använd kommandot om du vill `az provider list` se om providern är registrerad.

```azurecli-interactive
az provider list
```

Om du vill registrera `az provider register` en resursprovider använder du kommandot och anger *det namnområde som* ska registreras.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Om du vill visa platser och API-versioner som stöds för en resurstyp använder du:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Lösning 3 - Azure-portal

Du kan se registreringsstatus och registrera en resursleverantörsnamnområde via portalen.

1. Välj **Alla tjänster**på portalen .

   ![Välj alla tjänster](./media/error-register-resource-provider/select-all-services.png)

1. Välj **Prenumerationer**.

   ![Välj prenumerationer](./media/error-register-resource-provider/select-subscriptions.png)

1. Välj den prenumeration som du vill använda för att registrera resursleverantören i listan över prenumerationer.

   ![Välj prenumeration för att registrera resursprovider](./media/error-register-resource-provider/select-subscription-to-register.png)

1. För din prenumeration väljer du **Resursleverantörer**.

   ![Välj resursleverantörer](./media/error-register-resource-provider/select-resource-provider.png)

1. Titta på listan över resursleverantörer och välj vid behov länken **Registrera** för att registrera resursprovidern av den typ som du försöker distribuera.

   ![Lista resursleverantörer](./media/error-register-resource-provider/list-resource-providers.png)
