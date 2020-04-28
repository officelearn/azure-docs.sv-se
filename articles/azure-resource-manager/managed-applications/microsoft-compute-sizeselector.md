---
title: SizeSelector-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Compute. SizeSelector för Azure Portal. Används för att välja storlek på en virtuell dator.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652052"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Compute. SizeSelector

En kontroll för att välja en storlek för en eller flera virtuella dator instanser.

## <a name="ui-sample"></a>UI-exempel

Användaren ser en selektor med standardvärden från element definitionen.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

När du har valt kontrollen ser användaren en utökad vy av de tillgängliga storlekarna.

![Microsoft. Compute. SizeSelector utökad](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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

- `recommendedSizes`måste ha minst en storlek. Den första rekommenderade storleken används som standard. Listan över tillgängliga storlekar sorteras inte efter rekommenderat tillstånd. Användaren kan välja den kolumnen för att sortera efter rekommenderat tillstånd.
- Om en rekommenderad storlek inte är tillgänglig på den valda platsen hoppas storleken automatiskt över. I stället används nästa rekommenderade storlek.
- `constraints.allowedSizes`och `constraints.excludedSizes` är både valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan bestämmas genom att [listan med tillgängliga storlekar för virtuella datorer anropas för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Alla storlekar som inte anges i `constraints.allowedSizes` är dolda och den storlek som inte anges i `constraints.excludedSizes` visas.
- `osPlatform`måste anges och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa maskin varu kostnaderna för de virtuella datorerna.
- `imageReference`utelämnas för avbildningar från första part, men tillhandahålls för avbildningar från tredje part. Den används för att fastställa program varu kostnaderna för de virtuella datorerna.
- `count`används för att ange lämplig multiplikator för elementet. Det stöder ett statiskt värde, till exempel **2**, eller ett dynamiskt värde från ett annat `[steps('step1').vmCount]`element, t. ex.. Standardvärdet är **1**.
- `numAvailabilityZonesRequired` Kan vara 1, 2 eller 3.
- Som standard `hideDiskTypeFilter` är **falskt**. Filtret disk typ gör att användaren kan se alla disk typer eller endast SSD.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
