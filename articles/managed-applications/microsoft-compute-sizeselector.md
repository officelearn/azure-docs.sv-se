---
title: Azure SizeSelector UI-element | Microsoft Docs
description: Beskriver Microsoft.Compute.SizeSelector UI-element för Azure-portalen.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e5be5635964ebeedc7be4d1d1f5403e4d281b55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251314"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI-element
En kontroll för att välja en storlek för en eller flera instanser av virtuella datorer.

## <a name="ui-sample"></a>UI-exempel

Användaren ser en väljare med standardvärden från elementdefinition.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

När du har valt kontrollen, ser användaren en utökad vy över tillgängliga storlekar.

![Microsoft.Compute.SizeSelector expanderas](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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

## <a name="remarks"></a>Kommentarer
- `recommendedSizes` bör ha minst en storlek. Den första rekommenderade storleken används som standard. Listan över tillgängliga storlekar är inte sorteras efter det rekommenderade tillståndet. Användaren kan välja kolumnen att sortera efter rekommenderade tillstånd.
- Om en rekommenderad storlek inte är tillgängligt på den valda platsen är storleken automatiskt att hoppas över. I stället används nästa Rekommenderad storlek.
- `constraints.allowedSizes` och `constraints.excludedSizes` båda är valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan fastställas genom att anropa [lista över tillgängliga virtuella datorstorlekar för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Valfri storlek som inte har angetts i den `constraints.allowedSizes` är dolt och inte har angetts i storlekar `constraints.excludedSizes` visas.
- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa kostnader för maskinvara för virtuella datorer.
- `imageReference` tas bort för Förstaparts-avbildningar, men tillhandahållna för tredje parts-avbildningar. Den används för att fastställa kostnader för programvara för virtuella datorer.
- `count` används för att ange lämplig multiplikatorn för elementet. Den stöder ett statiskt värde som **2**, eller ett dynamiskt värde från ett annat element som `[steps('step1').vmCount]`. Standardvärdet är **1**.
- Den `numAvailabilityZonesRequired` kan vara 1, 2 eller 3.
- Som standard `hideDiskTypeFilter` är **FALSKT**. Disk Typfiltret gör det möjligt för användare att se alla disktyper eller endast SSD.

## <a name="sample-output"></a>Exempel på utdata
```json
"Standard_D1"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).
