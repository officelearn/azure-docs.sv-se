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
ms.openlocfilehash: 9009d29e281ace179ad1dd2021c7cf35e3dc611a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084815"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI-element
En kontroll för att välja en storlek för en eller flera instanser av virtuell dator.

## <a name="ui-sample"></a>UI-exempel

Användaren ser en väljare med standardvärden från elementdefinition.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

När du har valt kontrollen, ser användaren en utökad visning av tillgängliga storlekar.

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
- `recommendedSizes` bör ha minst en storlek. Den första rekommenderade storleken används som standard. Listan över tillgängliga storlekar är inte sorterad efter rekommenderade tillstånd. Användaren kan välja att kolumn att sortera efter rekommenderade tillstånd.
- Om en rekommenderade storleken är inte tillgänglig i den valda platsen storlek automatiskt att hoppas över. I stället används nästa Rekommenderad storlek.
- `constraints.allowedSizes` och `constraints.excludedSizes` både valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan fastställas genom att anropa [visa en lista med tillgängliga virtuella datorstorlekar för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Oavsett storlek inte har angetts i den `constraints.allowedSizes` är dolt och valfri storlek inte har angetts i `constraints.excludedSizes` visas.
- `osPlatform` måste anges, och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa kostnader för maskinvara för virtuella datorer.
- `imageReference` tas bort för från första part bilder, men det angivna för bilder från tredje part. Den används för att fastställa programvara kostnaderna för virtuella datorer.
- `count` används för att ange lämplig multiplikatorn för elementet. Den stöder ett statiskt värde som **2**, eller ett dynamiskt värde från ett annat element som `[steps('step1').vmCount]`. Standardvärdet är **1**.
- Den `numAvailabilityZonesRequired` kan vara 1, 2 eller 3.
- Som standard `hideDiskTypeFilter` är **FALSKT**. Disk typen filtret kan användaren att se alla disktyper eller endast SSD.

## <a name="sample-output"></a>Exempel på utdata
```json
"Standard_D1"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).
