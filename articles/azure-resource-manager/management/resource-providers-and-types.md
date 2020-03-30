---
title: Resursleverantörer och resurstyper
description: Beskriver de resursleverantörer som stöder Resource Manager, deras scheman och tillgängliga API-versioner och de regioner som kan vara värdar för resurserna.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273987"
---
# <a name="azure-resource-providers-and-types"></a>Resursproviders och resurstyper i Azure

När du distribuerar resurser måste du ofta hämta information om resursleverantörer och resurstyper. Om du till exempel vill lagra nycklar och hemligheter arbetar du med resursprovidern Microsoft.KeyVault. Den här resursprovidern erbjuder en resurstyp som heter vaults (valv) för att skapa nyckelvalvet.

Namnet på en resurstyp skrivs i formatet: **{resursprovidern}/{resurstypen}**. Resurstypen för ett nyckelvalv är **Microsoft.KeyVault\vaults**.

I den här artikeln kan du se hur du:

* Visa alla resursleverantörer i Azure
* Kontrollera en resursproviders registreringsstatus
* Registrera en resursprovider
* Visa resurstyper för en resursprovider
* Visa giltiga platser för en resurstyp
* Visa giltiga API-versioner för en resurstyp

Du kan göra dessa steg via Azure-portalen, Azure PowerShell eller Azure CLI.

En lista som mappar resursleverantörer till Azure-tjänster finns i [Resursleverantörer för Azure-tjänster](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Azure Portal

Så här visar du alla resursleverantörer och registreringsstatus för din prenumeration:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på menyn i Azure-portalen.

    ![välj prenumerationer](./media/resource-providers-and-types/select-all-services.png)

3. I rutan **Alla tjänster** anger du **prenumeration**och väljer sedan **Prenumerationer**.
4. Välj den prenumeration i prenumerationslistan som ska visas.
5. Välj **Resursleverantörer** och visa listan över tillgängliga resursleverantörer.

    ![visa resursleverantörer](./media/resource-providers-and-types/show-resource-providers.png)

6. Om du registrerar en resursleverantör konfigureras prenumerationen så att den fungerar med resursleverantören. Utrymmet för registrering är alltid prenumerationen. Som standard registreras många resursleverantörer automatiskt. Du kan dock behöva registrera vissa resursleverantörer manuellt. Om du vill registrera en resursprovider `/register/action` måste du ha behörighet att utföra åtgärden för resursleverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare. Om du vill registrera en resursleverantör väljer du **Registrera**. I föregående skärmbild markeras **länken Registrera** för **Microsoft.Blueprint**.

    Du kan inte avregistrera en resursleverantör när du fortfarande har resurstyper från resursleverantören i prenumerationen.

Så här visar du information för en viss resursleverantör:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på menyn i Azure-portalen.
3. I rutan **Alla tjänster** anger du **resursutforskaren**och väljer sedan **Resursutforskaren**.

    ![välj Alla tjänster](./media/resource-providers-and-types/select-resource-explorer.png)

4. Expandera **leverantörer** genom att välja högerpilen.

    ![Välj leverantörer](./media/resource-providers-and-types/select-providers.png)

5. Expandera en resursleverantör och resurstyp som du vill visa.

    ![Välj resurstyp](./media/resource-providers-and-types/select-resource-type.png)

6. Resource Manager stöds i alla regioner, men de resurser som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar för din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen. Resursutforskaren visar giltiga platser för resurstypen.

    ![Visa platser](./media/resource-providers-and-types/show-locations.png)

7. API-versionen motsvarar en version av REST API-åtgärder som frisläpps av resursprovidern. Eftersom en resursprovider aktiverar nya funktioner, den släpper en ny version av REST API. Resursutforskaren visar giltiga API-versioner för resurstypen.

    ![Visa API-versioner](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill visa alla resursleverantörer i Azure och registreringsstatusen för din prenumeration använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Som returnerar resultat som liknar:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Om du registrerar en resursleverantör konfigureras prenumerationen så att den fungerar med resursleverantören. Utrymmet för registrering är alltid prenumerationen. Som standard registreras många resursleverantörer automatiskt. Du kan dock behöva registrera vissa resursleverantörer manuellt. Om du vill registrera en resursprovider `/register/action` måste du ha behörighet att utföra åtgärden för resursleverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Som returnerar resultat som liknar:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Du kan inte avregistrera en resursleverantör när du fortfarande har resurstyper från resursleverantören i prenumerationen.

Om du vill visa information för en viss resursleverantör använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Som returnerar resultat som liknar:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Om du vill visa resurstyperna för en resursprovider använder du:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Som returnerar:

```output
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST API-åtgärder som frisläpps av resursprovidern. Eftersom en resursprovider aktiverar nya funktioner, den släpper en ny version av REST API.

Om du vill hämta tillgängliga API-versioner för en resurstyp använder du:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Som returnerar:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager stöds i alla regioner, men de resurser som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar för din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen.

Om du vill hämta de platser som stöds för en resurstyp använder du.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Som returnerar:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Om du vill visa alla resursleverantörer i Azure och registreringsstatusen för din prenumeration använder du:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Som returnerar resultat som liknar:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Om du registrerar en resursleverantör konfigureras prenumerationen så att den fungerar med resursleverantören. Utrymmet för registrering är alltid prenumerationen. Som standard registreras många resursleverantörer automatiskt. Du kan dock behöva registrera vissa resursleverantörer manuellt. Om du vill registrera en resursprovider `/register/action` måste du ha behörighet att utföra åtgärden för resursleverantören. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurecli
az provider register --namespace Microsoft.Batch
```

Som returnerar ett meddelande om att registrering pågår.

Du kan inte avregistrera en resursleverantör när du fortfarande har resurstyper från resursleverantören i prenumerationen.

Om du vill visa information för en viss resursleverantör använder du:

```azurecli
az provider show --namespace Microsoft.Batch
```

Som returnerar resultat som liknar:

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

Om du vill visa resurstyperna för en resursprovider använder du:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Som returnerar:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST API-åtgärder som frisläpps av resursprovidern. Eftersom en resursprovider aktiverar nya funktioner, den släpper en ny version av REST API.

Om du vill hämta tillgängliga API-versioner för en resurstyp använder du:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Som returnerar:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager stöds i alla regioner, men de resurser som du distribuerar kanske inte stöds i alla regioner. Dessutom kan det finnas begränsningar för din prenumeration som hindrar dig från att använda vissa regioner som stöder resursen.

Om du vill hämta de platser som stöds för en resurstyp använder du.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Som returnerar:

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

* Mer information om hur du skapar Resource Manager-mallar finns i [Skapa Azure Resource Manager-mallar](../templates/template-syntax.md). 
* Information om hur du visar resursprovidermallens scheman finns i [Mallreferens](/azure/templates/).
* En lista som mappar resursleverantörer till Azure-tjänster finns i [Resursleverantörer för Azure-tjänster](azure-services-resource-providers.md).
* Information om hur du visar åtgärderna för en resursprovider finns i [Azure REST API](/rest/api/).
