---
title: Azure-resurs-providers och resurstyper | Microsoft Docs
description: "Beskriver resursleverantörer som har stöd för hanteraren för filserverresurser, deras scheman och tillgängliga API-versioner och regioner som kan vara värd för resurser."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 6b1915288feff8f1d9fc780fef40a247d1c35839
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="resource-providers-and-types"></a>Resursproviders och typer

När du distribuerar resurser kan behöver du ofta hämta information om resursproviders och typer. I den här artikeln får du lära dig att:

* Visa alla providrar i Azure
* Kontrollera registreringsstatus av en resursprovider
* En registerresursleverantören
* Visa resurstyper för en resursprovider
* Visa giltiga platser för en resurstyp
* Visa giltiga API-versioner för en resurstyp

Du kan utföra dessa steg via portalen, PowerShell eller Azure CLI.

## <a name="powershell"></a>PowerShell

Om du vill se alla providrar i Azure och registreringsstatus för din prenumeration, använder du:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
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

Registrera en resursleverantör konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Många resursproviders registreras automatiskt som standard. Du kan dock behöva registrera manuellt vissa resursleverantörer. Om du vill registrera en resursleverantör, du måste ha behörighet att utföra den `/register/action` åtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Som returnerar resultat liknar:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Du kan inte avregistrera en resursleverantör när du har fortfarande resurstyper från resursprovidern i din prenumeration.

Informationen för en viss resurs-provider, Använd:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Som returnerar resultat liknar:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Om du vill visa resurstyperna för en resursleverantör, använder du:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Som returnerar:

```powershell
batchAccounts
operations
locations
locations/quotas
```

API-versionen motsvarar en version av REST-API: et som ges ut av resursprovidern. Som en resursleverantör aktiverar nya funktioner, släpper en ny version av REST API. 

Om du vill hämta tillgängliga API-versioner för en resurstyp, använder du:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Som returnerar:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Hanteraren för filserverresurser stöds i alla regioner, men de resurser som du distribuerar stöds inte i alla regioner. Dessutom kan finnas det begränsningar för din prenumeration som hindrar dig från att använda vissa regioner som har stöd för resursen. 

Använd följande för att få placeringar som stöds för en resurstyp.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
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
Om du vill se alla providrar i Azure och registreringsstatus för din prenumeration, använder du:

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

Registrera en resursleverantör konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Många resursproviders registreras automatiskt som standard. Du kan dock behöva registrera manuellt vissa resursleverantörer. Om du vill registrera en resursleverantör, du måste ha behörighet att utföra den `/register/action` åtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare.

```azurecli
az provider register --namespace Microsoft.Batch
```

Som returnerar ett meddelande som registrering är pågående.

Du kan inte avregistrera en resursleverantör när du har fortfarande resurstyper från resursprovidern i din prenumeration.

Informationen för en viss resurs-provider, Använd:

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

Om du vill visa resurstyperna för en resursleverantör, använder du:

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

API-versionen motsvarar en version av REST-API: et som ges ut av resursprovidern. Som en resursleverantör aktiverar nya funktioner, släpper en ny version av REST API. 

Om du vill hämta tillgängliga API-versioner för en resurstyp, använder du:

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

Hanteraren för filserverresurser stöds i alla regioner, men de resurser som du distribuerar stöds inte i alla regioner. Dessutom kan finnas det begränsningar för din prenumeration som hindrar dig från att använda vissa regioner som har stöd för resursen. 

Använd följande för att få placeringar som stöds för en resurstyp.

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

## <a name="portal"></a>Portalen

Om du vill se alla providrar i Azure och registreringsstatus för din prenumeration, Välj **prenumerationer**.

![Välj prenumerationer](./media/resource-manager-supported-services/select-subscriptions.png)

Välj prenumerationen att visa.

![Ange prenumeration](./media/resource-manager-supported-services/subscription.png)

Välj **resursproviders** och visa listan över tillgängliga resursproviders.

![Visa resursprovidrar](./media/resource-manager-supported-services/show-resource-providers.png)

Registrera en resursleverantör konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Många resursproviders registreras automatiskt som standard. Du kan dock behöva registrera manuellt vissa resursleverantörer. Om du vill registrera en resursleverantör, du måste ha behörighet att utföra den `/register/action` åtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Om du vill registrera en resursleverantör, Välj **registrera**.

![registerresursleverantören](./media/resource-manager-supported-services/register-provider.png)

Du kan inte avregistrera en resursleverantör när du har fortfarande resurstyper från resursprovidern i din prenumeration.

Om du vill se informationen för en viss resurs-provider väljer **alla tjänster**.

![Markera alla tjänster](./media/resource-manager-supported-services/more-services.png)

Sök efter **Resursläsaren** och välj bland de tillgängliga alternativen.

![Välj resursläsaren](./media/resource-manager-supported-services/select-resource-explorer.png)

Välj **Providers**.

![Välj providers](./media/resource-manager-supported-services/select-providers.png)

Välj resursprovidern och resurstypen som du vill visa.

![Välj resurstyp](./media/resource-manager-supported-services/select-resource-type.png)

Hanteraren för filserverresurser stöds i alla regioner, men de resurser som du distribuerar stöds inte i alla regioner. Dessutom kan finnas det begränsningar för din prenumeration som hindrar dig från att använda vissa regioner som har stöd för resursen. Resursläsaren visar giltiga platser för resurstypen.

![Visa platser](./media/resource-manager-supported-services/show-locations.png)

API-versionen motsvarar en version av REST-API: et som ges ut av resursprovidern. Som en resursleverantör aktiverar nya funktioner, släpper en ny version av REST API. Resursläsaren visar giltiga API-versioner för resurstypen.

![Visa API-versioner](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skapar Resource Manager-mallar i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Läs om hur du distribuerar resurser i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).
* Åtgärder för en resursleverantör finns [Azure REST API](/rest/api/).

