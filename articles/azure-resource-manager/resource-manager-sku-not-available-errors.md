---
title: 'Azure SKU: N inte tillgänglig fel | Microsoft Docs'
description: 'Beskriver hur du felsöker SKU: N inte tillgänglig fel under distributionen.'
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
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 1dd0532452c3558e53f0236998953d2055ed328c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489705"
---
# <a name="resolve-errors-for-sku-not-available"></a>Åtgärda fel för SKU: N inte tillgänglig

Den här artikeln beskriver hur du löser det **SkuNotAvailable** fel. Om det inte går att hitta en lämplig SKU i regionen eller en alternativ region som uppfyller din verksamhet behöver, skicka en [SKU begäran](https://aka.ms/skurestriction) till Azure-supporten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symtom

När du distribuerar en resurs (vanligtvis en virtuell dator) kan få du följande felkod och felmeddelande:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Orsak

Du får detta felmeddelande när resursen SKU som du har valt (till exempel VM-storlek) är inte tillgängligt för den plats som du har valt.

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

Om du vill ta reda på vilken SKU: er är tillgängliga i en region, Använd den [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) kommando. Filtrera resultatet efter plats. Du måste ha den senaste versionen av PowerShell för det här kommandot.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Resultatet innehåller en lista över SKU: er för platsen och eventuella begränsningar för SKU: N. Observera att en SKU kan stå som `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Lösning 2 – Azure CLI

Om du vill ta reda på vilken SKU: er är tillgängliga i en region, Använd den `az vm list-skus` kommando. Använd den `--location` parameter för att filtrera utdata till plats som du använder. Använd den `--size` parametern för att söka efter en partiell storleksnamn.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Kommandot returnerar resultat:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Lösning 3 – Azure-portalen

Om du vill ta reda på vilken SKU: er är tillgängliga i en region, Använd den [portal](https://portal.azure.com). Logga in på portalen och lägga till en resurs via gränssnittet. När du har angett värden visas tillgängliga SKU: er för den resursen. Du behöver inte slutföra distributionen.

Till exempel starta processen med att skapa en virtuell dator. Om du vill se andra tillgängliga storleken **ändra storleken på**.

![Skapa en virtuell dator](./media/resource-manager-sku-not-available-errors/create-vm.png)

Du kan filtrera och bläddra igenom tillgängliga storlekar.

![Tillgängliga SKU: er](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Lösningen 4 – REST

Om du vill ta reda på vilken SKU: er är tillgängliga i en region, Använd den [SKU: er – lista](/rest/api/compute/resourceskus/list) igen.

Den returnerar tillgängliga SKU: er och regioner i följande format:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

