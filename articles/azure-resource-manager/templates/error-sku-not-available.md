---
title: SKU inte tillgängliga fel
description: Beskriver felsöka SKU-felet som inte är tillgängligt när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942716"
---
# <a name="resolve-errors-for-sku-not-available"></a>Åtgärda fel med otillgänglig SKU

I den här artikeln beskrivs hur du löser felet **SkuNotAvailable.** Om du inte kan hitta en lämplig SKU i den regionen/zonen eller en alternativ region/zon som uppfyller dina affärsbehov skickar du en [SKU-begäran](https://aka.ms/skurestriction) till Azure Support.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

När du distribuerar en resurs (vanligtvis en virtuell dator) visas följande felmeddelande och felmeddelande:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Orsak

Det här felet visas när resursen SKU som du har valt (till exempel vm-storlek) inte är tillgänglig för den plats du har valt.

Om du distribuerar en Azure Spot VM- eller Spot-skalningsuppsättningsinstans finns det ingen kapacitet för Azure Spot på den här platsen. Mer information finns i [Ta ut felmeddelanden](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Lösning 1 - PowerShell

Om du vill ta reda på vilka SKU:er som är tillgängliga i en region/zon använder du kommandot [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filtrera resultaten efter plats. Du måste ha den senaste versionen av PowerShell för det här kommandot.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Resultaten innehåller en lista över SKU:er för platsen och eventuella begränsningar för den SKU:n. Observera att en SKU kan `NotAvailableForSubscription`visas som .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Några ytterligare exempel:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

40 "fc" i slutet returnerar mer information.

## <a name="solution-2---azure-cli"></a>Lösning 2 - Azure CLI

Om du vill ta reda på `az vm list-skus` vilka SKU:er som är tillgängliga i en region använder du kommandot. Använd `--location` parametern för att filtrera utdata till den plats du använder. Använd `--size` parametern för att söka efter ett partiellt storleksnamn.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Kommandot returnerar resultat som:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Lösning 3 - Azure-portal

Om du vill ta reda på vilka SKU:er som är tillgängliga i en region använder du [portalen](https://portal.azure.com). Logga in på portalen och lägg till en resurs via gränssnittet. När du anger värdena visas tillgängliga SKU:er för den resursen. Du behöver inte slutföra distributionen.

Starta till exempel processen med att skapa en virtuell dator. Om du vill se annan tillgänglig storlek väljer du **Ändra storlek**.

![Skapa en virtuell dator](./media/error-sku-not-available/create-vm.png)

Du kan filtrera och bläddra igenom de tillgängliga storlekarna.

![Tillgängliga SKU:er](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Lösning 4 - REST

Om du vill ta reda på vilka SKU:er som är tillgängliga i en region använder du åtgärden [Resursskäus - Lista.](/rest/api/compute/resourceskus/list)

Den returnerar tillgängliga SKU:er och regioner i följande format:

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

