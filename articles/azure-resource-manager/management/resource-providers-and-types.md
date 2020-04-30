---
title: Resurs leverantörer och resurs typer
description: Beskriver de resurs leverantörer som stöder Resource Manager, deras scheman och tillgängliga API-versioner och de regioner som kan vara värdar för resurserna.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79273987"
---
# <a name="azure-resource-providers-and-types"></a>Resursproviders och resurstyper i Azure

När du distribuerar resurser behöver du ofta hämta information om resurs leverantörer och typer. Om du till exempel vill lagra nycklar och hemligheter arbetar du med resursprovidern Microsoft.KeyVault. Den här resursprovidern erbjuder en resurstyp som heter vaults (valv) för att skapa nyckelvalvet.

Namnet på en resurstyp skrivs i formatet: **{resursprovidern}/{resurstypen}**. Resurstypen för ett nyckelvalv är **Microsoft.KeyVault\vaults**.

I den här artikeln kan du se hur du:

* Visa alla resurs leverantörer i Azure
* Kontrol lera registrerings status för en resurs leverantör
* Registrera en resursprovider
* Visa resurs typer för en resurs leverantör
* Visa giltiga platser för en resurs typ
* Visa giltiga API-versioner för en resurs typ

Du kan utföra de här stegen via Azure Portal, Azure PowerShell eller Azure CLI.

En lista som mappar resurs leverantörer till Azure-tjänster finns i [Resource providers för Azure-tjänster](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Azure Portal

Så här visar du alla resurs leverantörer och registrerings status för din prenumeration:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på menyn i Azure-portalen.

    ![Välj prenumerationer](./media/resource-providers-and-types/select-all-services.png)

3. I rutan **alla tjänster** anger du **prenumeration**och väljer sedan **prenumerationer**.
4. Välj den prenumeration i listan prenumeration som du vill visa.
5. Välj **resurs leverantörer** och Visa listan över tillgängliga resurs leverantörer.

    ![Visa resurs leverantörer](./media/resource-providers-and-types/show-resource-providers.png)

6. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Omfånget för registreringen är alltid prenumerationen. Som standard registreras många resurs leverantörer automatiskt. Du kan dock behöva registrera vissa resurs leverantörer manuellt. Om du vill registrera en resurs leverantör måste du ha behörighet att utföra `/register/action` åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera en resurs leverantör genom att välja **Registrera**. I föregående skärm bild är **register** länken markerad för **Microsoft. skiss**.

    Du kan inte avregistrera en resurs leverantör när du fortfarande har resurs typer från den resurs leverantören i din prenumeration.

Så här visar du information om en viss resurs leverantör:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på menyn i Azure-portalen.
3. I rutan **alla tjänster** anger du **resurs läsaren**och väljer sedan **Resursläsaren**.

    ![Välj alla tjänster](./media/resource-providers-and-types/select-resource-explorer.png)

4. Expandera **providers** genom att välja den högra pilen.

    ![Välj leverantörer](./media/resource-providers-and-types/select-providers.png)

5. Expandera en resurs leverantör och resurs typ som du vill visa.

    ![Välj resurs typ](./media/resource-providers-and-types/select-resource-type.png)

6. Resource Manager stöds i alla regioner, men resurserna som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar i din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen. Resurs läsaren visar giltiga platser för resurs typen.

    ![Visa platser](./media/resource-providers-and-types/show-locations.png)

7. API-versionen motsvarar en version av REST API åtgärder som släpps av resurs leverantören. När en resurs leverantör aktiverar nya funktioner, släpps en ny version av REST API. Resurs läsaren visar giltiga API-versioner för resurs typen.

    ![Visa API-versioner](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill se alla resurs leverantörer i Azure och registrerings status för din prenumeration använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Vilket returnerar resultat som liknar:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Omfånget för registreringen är alltid prenumerationen. Som standard registreras många resurs leverantörer automatiskt. Du kan dock behöva registrera vissa resurs leverantörer manuellt. Om du vill registrera en resurs leverantör måste du ha behörighet att utföra `/register/action` åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Vilket returnerar resultat som liknar:

```output
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

```output
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

```output
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

```output
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

```output
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

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Omfånget för registreringen är alltid prenumerationen. Som standard registreras många resurs leverantörer automatiskt. Du kan dock behöva registrera vissa resurs leverantörer manuellt. Om du vill registrera en resurs leverantör måste du ha behörighet att utföra `/register/action` åtgärden för resurs leverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare.

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

```output
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

```output
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

```output
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

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager mallar](../templates/template-syntax.md). 
* Om du vill visa resurs leverantörens mall scheman, se [referens för mallar](/azure/templates/).
* En lista som mappar resurs leverantörer till Azure-tjänster finns i [Resource providers för Azure-tjänster](azure-services-resource-providers.md).
* Information om hur du visar åtgärder för en resurs leverantör finns i [Azure REST API](/rest/api/).
