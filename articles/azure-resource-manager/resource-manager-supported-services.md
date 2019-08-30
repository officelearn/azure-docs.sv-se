---
title: Azure-resurs-providers och resurs typer | Microsoft Docs
description: Beskriver de resurs leverantörer som stöder Resource Manager, deras scheman och tillgängliga API-versioner och de regioner som kan vara värdar för resurserna.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 2cbc8843d41b760c52b9ca5ccfb6d940bd454136
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164809"
---
# <a name="azure-resource-providers-and-types"></a>Azure-resurs leverantörer och-typer

När du distribuerar resurser behöver du ofta hämta information om resurs leverantörer och typer. Om du till exempel vill lagra nycklar och hemligheter arbetar du med resurs leverantören Microsoft. nyckel valv. Den här resurs leverantören erbjuder en resurs typ som kallas valv för att skapa nyckel valvet.

Namnet på en resurstyp skrivs i formatet: **{resursprovidern}/{resurstypen}** . Resurstypen för ett nyckelvalv är **Microsoft.KeyVault\vaults**.

I den här artikeln kan du se hur du:

* Visa alla resurs leverantörer i Azure
* Kontrol lera registrerings status för en resurs leverantör
* Registrera en resurs leverantör
* Visa resurs typer för en resurs leverantör
* Visa giltiga platser för en resurs typ
* Visa giltiga API-versioner för en resurs typ

Du kan utföra de här stegen via Azure Portal, Azure PowerShell eller Azure CLI.

En lista som mappar resurs leverantörer till Azure-tjänster finns i [Resource providers för Azure-tjänster](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Azure Portal

Så här visar du alla resurs leverantörer och registrerings status för din prenumeration:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster**.

    ![Välj prenumerationer](./media/resource-manager-supported-services/select-subscriptions.png)
3. I rutan **alla tjänster** anger du **prenumeration**och väljer sedan prenumerationer.
4. Välj den prenumeration i listan prenumeration som du vill visa.
5. Välj **resurs leverantörer** och Visa listan över tillgängliga resurs leverantörer.

    ![Visa resurs leverantörer](./media/resource-manager-supported-services/show-resource-providers.png)

6. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Omfattningen för registrering är alltid prenumerationen. Som standard registreras många resurs leverantörer automatiskt. Du kan dock behöva registrera vissa resurs leverantörer manuellt. Om du vill registrera en resurs leverantör måste du ha behörighet att utföra `/register/action` åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera en resurs leverantör genom att välja **Registrera**. I föregående skärm bild är **register** länken markerad för **Microsoft. skiss**.

    Du kan inte avregistrera en resurs leverantör när du fortfarande har resurs typer från den resurs leverantören i din prenumeration.

Så här visar du information om en viss resurs leverantör:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster**.

    ![Välj alla tjänster](./media/resource-manager-supported-services/more-services.png)

3. I rutan **alla tjänster** anger du **resurs läsaren**och väljer sedan **Resursläsaren**.
4. Expandera **providers** genom att välja den högra pilen.

    ![Välj leverantörer](./media/resource-manager-supported-services/select-providers.png)

5. Expandera en resurs leverantör och resurs typ som du vill visa.

    ![Välj resurs typ](./media/resource-manager-supported-services/select-resource-type.png)

6. Resource Manager stöds i alla regioner, men resurserna som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar i din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen. Resurs läsaren visar giltiga platser för resurs typen.

    ![Visa platser](./media/resource-manager-supported-services/show-locations.png)

7. API-versionen motsvarar en version av REST API åtgärder som släpps av resurs leverantören. När en resurs leverantör aktiverar nya funktioner, släpps en ny version av REST API. Resurs läsaren visar giltiga API-versioner för resurs typen.

    ![Visa API-versioner](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill se alla resurs leverantörer i Azure och registrerings status för din prenumeration använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Vilket returnerar resultat som liknar:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Omfattningen för registrering är alltid prenumerationen. Som standard registreras många resurs leverantörer automatiskt. Du kan dock behöva registrera vissa resurs leverantörer manuellt. Om du vill registrera en resurs leverantör måste du ha behörighet att utföra `/register/action` åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Vilket returnerar resultat som liknar:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Du kan inte avregistrera en resurs leverantör när du fortfarande har resurs typer från den resurs leverantören i din prenumeration.

Om du vill se information om en viss resurs leverantör använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Vilket returnerar resultat som liknar:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Om du vill se resurs typerna för en resurs leverantör använder du:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Returnerar:

```powershell
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST API åtgärder som släpps av resurs leverantören. När en resurs leverantör aktiverar nya funktioner, släpps en ny version av REST API.

Använd följande för att hämta tillgängliga API-versioner för en resurs typ:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Returnerar:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager stöds i alla regioner, men resurserna som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar i din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen.

Använd för att hämta de platser som stöds för en resurs typ.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Returnerar:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Om du vill se alla resurs leverantörer i Azure och registrerings status för din prenumeration använder du:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Vilket returnerar resultat som liknar:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Omfattningen för registrering är alltid prenumerationen. Som standard registreras många resurs leverantörer automatiskt. Du kan dock behöva registrera vissa resurs leverantörer manuellt. Om du vill registrera en resurs leverantör måste du ha behörighet att utföra `/register/action` åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurecli
az provider register --namespace Microsoft.Batch
```

Som returnerar ett meddelande om att registreringen pågår.

Du kan inte avregistrera en resurs leverantör när du fortfarande har resurs typer från den resurs leverantören i din prenumeration.

Om du vill se information om en viss resurs leverantör använder du:

```azurecli
az provider show --namespace Microsoft.Batch
```

Vilket returnerar resultat som liknar:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Om du vill se resurs typerna för en resurs leverantör använder du:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Returnerar:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST API åtgärder som släpps av resurs leverantören. När en resurs leverantör aktiverar nya funktioner, släpps en ny version av REST API.

Använd följande för att hämta tillgängliga API-versioner för en resurs typ:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Returnerar:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager stöds i alla regioner, men resurserna som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar i din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen.

Använd för att hämta de platser som stöds för en resurs typ.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Returnerar:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager mallar](resource-group-authoring-templates.md). 
* Om du vill visa resurs leverantörens mall scheman, se [referens för mallar](/azure/templates/).
* En lista som mappar resurs leverantörer till Azure-tjänster finns i [Resource providers för Azure-tjänster](azure-services-resource-providers.md).
* Information om hur du visar åtgärder för en resurs leverantör finns i [Azure REST API](/rest/api/).
