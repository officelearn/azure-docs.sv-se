---
title: Gränssnittselement för SizeSelector
description: I artikeln beskrivs användargränssnittselementet Microsoft.Compute.SizeSelector för Azure portal. Används för att välja storleken på en virtuell dator.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652052"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Användargränssnittselementet Microsoft.Compute.SizeSelector

En kontroll för att välja en storlek för en eller flera instanser av virtuella datorer.

## <a name="ui-sample"></a>Exempel på användargränssnitt

Användaren ser en väljare med standardvärden från elementdefinitionen.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

När du har valt kontrollen ser användaren en utökad vy över de tillgängliga storlekarna.

![Microsoft.Compute.SizeSelector expanderat](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
"Standard_D1"
```

## <a name="remarks"></a>Anmärkningar

- `recommendedSizes`bör ha minst en storlek. Den första rekommenderade storleken används som standard. Listan över tillgängliga storlekar sorteras inte efter det rekommenderade tillståndet. Användaren kan välja den kolumnen för att sortera efter rekommenderat tillstånd.
- Om en rekommenderad storlek inte är tillgänglig på den valda platsen hoppas storleken automatiskt över. I stället används nästa rekommenderade storlek.
- `constraints.allowedSizes`och `constraints.excludedSizes` är båda valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan bestämmas genom att anropa [Lista tillgängliga storlekar för virtuella datorer för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Alla storlekar som inte `constraints.allowedSizes` anges i är dolda och `constraints.excludedSizes` alla storlekar som inte anges i visas.
- `osPlatform`måste anges, och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa maskinvarukostnaderna för de virtuella datorerna.
- `imageReference`utelämnas för bilder från första part, men tillhandahålls för bilder från tredje part. Det används för att bestämma programvarukostnaderna för de virtuella datorerna.
- `count`används för att ställa in lämplig multiplikator för elementet. Den stöder ett statiskt värde, som **2**, `[steps('step1').vmCount]`eller ett dynamiskt värde från ett annat element, till exempel . Standardvärdet är **1**.
- Den `numAvailabilityZonesRequired` kan vara 1, 2 eller 3.
- Som standard `hideDiskTypeFilter` är **falskt**. Disktypfiltret gör det möjligt för användaren att se alla disktyper eller bara SSD.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
