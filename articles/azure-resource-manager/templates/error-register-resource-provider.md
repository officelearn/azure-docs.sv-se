---
title: Registrerings fel för resurs leverantör
description: Beskriver hur du löser registrerings fel i Azure Resource Provider när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390051"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Åtgärda fel för registreringen av resursprovidern

Den här artikeln beskriver de fel som kan uppstå när du använder en provider för nätverksresurser som du inte tidigare har använt i din prenumeration.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

Om du vill se om providern är registrerad använder du kommandot `az provider list`.

```azurecli-interactive
az provider list
```

Registrera en resurs leverantör genom att använda kommandot `az provider register` och ange *namn området* som ska registreras.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Om du vill se de platser som stöds och API-versioner för en resurstyp, använder du:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Lösning 3 – Azure-portalen

Du kan se registreringsstatus och registrerar en resursproviderns namnområde via portalen.

1. Från portalen väljer du **alla tjänster**.

   ![Välj alla tjänster](./media/error-register-resource-provider/select-all-services.png)

1. Välj **Prenumerationer**.

   ![Välj prenumerationer](./media/error-register-resource-provider/select-subscriptions.png)

1. Välj den prenumeration som du vill använda för att registrera resursprovidern i listan över prenumerationer.

   ![Välj prenumeration för att registrera resursprovidern](./media/error-register-resource-provider/select-subscription-to-register.png)

1. För din prenumeration väljer du **resurs leverantörer**.

   ![Välj resursprovidrar](./media/error-register-resource-provider/select-resource-provider.png)

1. Titta på listan över resurs leverantörer och om det behövs väljer du länken **Registrera** för att registrera resurs leverantören av den typ som du försöker distribuera.

   ![Lista resursprovidrar](./media/error-register-resource-provider/list-resource-providers.png)
