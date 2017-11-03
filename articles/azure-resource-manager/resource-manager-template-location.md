---
title: Azure resource-plats i mallen | Microsoft Docs
description: "Visar hur du anger en plats för en resurs i en Azure Resource Manager-mall"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Ange resursplats i Azure Resource Manager-mallar
När du distribuerar en mall måste du ange en plats för varje resurs. Det här avsnittet beskrivs hur du tar reda på platser som är tillgängliga för din prenumeration för varje resurstyp av.

## <a name="determine-supported-locations"></a>Fastställa platser som stöds

En fullständig lista över platser som stöds för varje resurstyp av finns [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/). Prenumerationen kanske dock inte har åtkomst till alla platser i listan. Använda Azure PowerShell eller Azure CLI om du vill se en egen lista över platser som är tillgängliga för din prenumeration. 

I följande exempel använder PowerShell för att hämta platser för den `Microsoft.Web\sites` resurstyp:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

I följande exempel använder Azure CLI 2.0 för att hämta platser för den `Microsoft.Web\sites` resurstyp:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>Ange plats i mallen

Du måste ange den platsen i mallen när du har angett placeringar som stöds för dina resurser. Det enklaste sättet att ange det här värdet är att skapa en resursgrupp i en plats som har stöd för resurstyperna och ange varje plats som `[resourceGroup().location]`. Du kan omdistribuera mallen till resursgrupper på olika platser och inte att ändra alla värden i mallen eller parametrar. 

I följande exempel visas ett lagringskonto som har distribuerats till samma plats som resursgruppen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Om du behöver att hårdkoda platsen i mallen kan du ange namnet på en av regionerna som stöds. I följande exempel visas ett lagringskonto som alltid har distribuerats till norra centrala USA:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>Nästa steg
* Rekommendationer om hur du skapar mallar finns i [bästa praxis för att skapa mallar för Azure Resource Manager](resource-manager-template-best-practices.md).

