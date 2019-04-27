---
title: Azure resursproviders och resurstyper | Microsoft Docs
description: Beskriver de resursprovidrar som stöd för Resource Manager, deras scheman och tillgängliga API-versioner och de regioner som kan vara värd för resurserna.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: tomfitz
ms.openlocfilehash: 520aeb8e47b5e94e6346e682f21f46cb0814f8f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389715"
---
# <a name="azure-resource-providers-and-types"></a>Azure resursproviders och resurstyper

När du distribuerar resurser kan behöva du ofta hämta information om resursproviders och resurstyper. I den här artikeln kan du se hur du:

* Visa alla resursprovidrar i Azure
* Kontrollera registreringsstatus för en resursprovider
* Registrera en resursleverantör
* Visa resurstyper för en resursprovider
* Visa giltiga platser för en resurstyp
* Visa giltig API-versioner för en resurstyp

Du kan utföra dessa steg via Azure-portalen, Azure PowerShell eller Azure CLI.

## <a name="azure-portal"></a>Azure Portal

Visa alla resursprovidrar och registreringsstatus för din prenumeration:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster**.

    ![Välj prenumerationer](./media/resource-manager-supported-services/select-subscriptions.png)
3. I den **alla tjänster** anger **prenumeration**, och välj sedan **prenumerationer**.
4. Välj prenumerationen i prenumerationslistan ska visa.
5. Välj **resursprovidrar** och visa en lista över tillgängliga resursproviders.

    ![Visa resursprovidrar](./media/resource-manager-supported-services/show-resource-providers.png)

6. Registrera en resursleverantör konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Många resursproviders registreras automatiskt som standard. Du kan dock behöva registrera några resursproviders manuellt. Om du vill registrera en resursleverantör måste du ha behörighet att göra den `/register/action` åtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Om du vill registrera en resursleverantör, Välj **registrera**. I föregående skärmbild kan den **registrera** länk markeras för **Microsoft.Blueprint**.

    Du kan inte avregistrera en resursprovider när du fortfarande har resurstyper från den resursprovidern i din prenumeration.

Visa information för en viss resursprovider:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster**.

    ![Välj alla tjänster](./media/resource-manager-supported-services/more-services.png)

3. I den **alla tjänster** anger **resursläsaren**, och välj sedan **Resursläsaren**.
4. Expandera **Providers** genom att välja pilen till höger.

    ![Välj providers](./media/resource-manager-supported-services/select-providers.png)

5. Expandera en provider för nätverksresurser och resurstyp som du vill visa.

    ![Välj resurstyp](./media/resource-manager-supported-services/select-resource-type.png)

6. Resource Manager stöds i alla regioner, men de resurser som du distribuerar stöds inte i alla regioner. Dessutom kan finnas det begränsningar av prenumerationen som hindrar dig från att använda vissa regioner som har stöd för resursen. Resursläsaren visar giltiga platser för resurstypen.

    ![Visa platser](./media/resource-manager-supported-services/show-locations.png)

7. API-versionen motsvarar en version av REST API-åtgärder som ges ut av resursprovidern. Eftersom en resursprovider ger nya funktioner, släpper en ny version av REST API. Resursläsaren visar giltig API-versioner för resurstypen.

    ![Visa API-versioner](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill se alla resursprovidrar i Azure och registreringsstatus för din prenumeration, använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Som returnerar resultat liknar:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registrera en resursleverantör konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Många resursproviders registreras automatiskt som standard. Du kan dock behöva registrera några resursproviders manuellt. Om du vill registrera en resursleverantör måste du ha behörighet att göra den `/register/action` åtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Som returnerar resultat liknar:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Du kan inte avregistrera en resursprovider när du fortfarande har resurstyper från den resursprovidern i din prenumeration.

Om du vill se information för en viss resursprovider, använder du:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Som returnerar resultat liknar:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Om du vill visa resurstyperna för en resursprovider, använder du:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Som returnerar:

```powershell
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST API-åtgärder som ges ut av resursprovidern. Eftersom en resursprovider ger nya funktioner, släpper en ny version av REST API.

Hämta tillgängliga API-versioner för en resurstyp med:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Som returnerar:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager stöds i alla regioner, men de resurser som du distribuerar stöds inte i alla regioner. Dessutom kan finnas det begränsningar av prenumerationen som hindrar dig från att använda vissa regioner som har stöd för resursen.

Använd följande för att få platser som stöds för en resurstyp.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Som returnerar:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Om du vill se alla resursprovidrar i Azure och registreringsstatus för din prenumeration, använder du:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Som returnerar resultat liknar:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registrera en resursleverantör konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Många resursproviders registreras automatiskt som standard. Du kan dock behöva registrera några resursproviders manuellt. Om du vill registrera en resursleverantör måste du ha behörighet att göra den `/register/action` åtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurecli
az provider register --namespace Microsoft.Batch
```

Som returnerar ett meddelande till denna registrering pågår.

Du kan inte avregistrera en resursprovider när du fortfarande har resurstyper från den resursprovidern i din prenumeration.

Om du vill se information för en viss resursprovider, använder du:

```azurecli
az provider show --namespace Microsoft.Batch
```

Som returnerar resultat liknar:

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

Om du vill visa resurstyperna för en resursprovider, använder du:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Som returnerar:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST API-åtgärder som ges ut av resursprovidern. Eftersom en resursprovider ger nya funktioner, släpper en ny version av REST API.

Hämta tillgängliga API-versioner för en resurstyp med:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Som returnerar:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager stöds i alla regioner, men de resurser som du distribuerar stöds inte i alla regioner. Dessutom kan finnas det begränsningar av prenumerationen som hindrar dig från att använda vissa regioner som har stöd för resursen.

Använd följande för att få platser som stöds för en resurstyp.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Som returnerar:

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

* Läs om hur du skapar Resource Manager-mallar i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md). 
* Mallscheman för resource provider finns [mallreferensen](/azure/templates/).
* Läs om hur du distribuerar resurser i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).
* Åtgärder för en resursprovider finns [Azure REST API](/rest/api/).
