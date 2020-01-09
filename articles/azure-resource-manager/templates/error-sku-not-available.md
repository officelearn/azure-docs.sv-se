---
title: SKU är inte tillgängliga, fel
description: Beskriver hur du felsöker fel meddelandet SKU är inte tillgängligt när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.openlocfilehash: a79f55b4d3baf33126807fa099ed2d7b8b48aac5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477465"
---
# <a name="resolve-errors-for-sku-not-available"></a>Lösa fel för SKU: n är inte tillgänglig

Den här artikeln beskriver hur du löser **SkuNotAvailable** -felet. Om du inte kan hitta en lämplig SKU i regionen eller en alternativ region som uppfyller dina affärs behov skickar du en SKU- [begäran](https://aka.ms/skurestriction) till Azure-supporten.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

När du distribuerar en resurs (vanligt vis en virtuell dator) visas följande felkod och fel meddelandet:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Orsak

Du får det här felet när resurs-SKU: n som du har valt (till exempel VM-storlek) inte är tillgänglig för den plats som du har valt.

Om du distribuerar en instans av en virtuell Azure-dator eller en punkt skalnings uppsättning finns det ingen kapacitet för Azure-platsen på den här platsen. Mer information finns i [fel meddelanden på plats](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Lösning 1 – PowerShell

Använd kommandot [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) för att avgöra vilka SKU: er som är tillgängliga i en region. Filtrera resultaten efter plats. Du måste ha den senaste versionen av PowerShell för det här kommandot.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Resultaten innehåller en lista över SKU: er för platsen och eventuella begränsningar för den SKU: n. Observera att en SKU kan visas som `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Lösning 2 – Azure CLI

Använd kommandot `az vm list-skus` för att avgöra vilka SKU: er som är tillgängliga i en region. Använd parametern `--location` för att filtrera utdata till den plats som du använder. Använd parametern `--size` för att söka efter ett namn för en partiell storlek.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Kommandot returnerar resultat som:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Lösning 3 – Azure-portalen

Använd [portalen](https://portal.azure.com)för att avgöra vilka SKU: er som är tillgängliga i en region. Logga in på portalen och Lägg till en resurs via gränssnittet. När du anger värden visas tillgängliga SKU: er för resursen. Du behöver inte slutföra distributionen.

Starta till exempel processen för att skapa en virtuell dator. Välj **ändra storlek**om du vill se annan tillgänglig storlek.

![Skapa en virtuell dator](./media/error-sku-not-available/create-vm.png)

Du kan filtrera och rulla igenom de tillgängliga storlekarna.

![Tillgängliga SKU:er](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Lösning 4 – REST

För att avgöra vilka SKU: er som är tillgängliga i en region, använder du åtgärden [Resource SKU-List](/rest/api/compute/resourceskus/list) .

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

