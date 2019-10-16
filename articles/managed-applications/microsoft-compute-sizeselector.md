---
title: Användar gränssnitts element för Azure SizeSelector | Microsoft Docs
description: Beskriver användar gränssnitts elementet Microsoft. Compute. SizeSelector för Azure Portal. Används för att välja storlek på en virtuell dator.
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
ms.openlocfilehash: 288ea7e887a170c8560b0126fa53c9132da35db6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332670"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Compute. SizeSelector
En kontroll för att välja en storlek för en eller flera virtuella dator instanser.

## <a name="ui-sample"></a>UI-exempel

Användaren ser en selektor med standardvärden från element definitionen.

![Microsoft. Compute. SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

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

## <a name="remarks"></a>Kommentarer
- `recommendedSizes` måste ha minst en storlek. Den första rekommenderade storleken används som standard. Listan över tillgängliga storlekar sorteras inte efter rekommenderat tillstånd. Användaren kan välja den kolumnen för att sortera efter rekommenderat tillstånd.
- Om en rekommenderad storlek inte är tillgänglig på den valda platsen hoppas storleken automatiskt över. I stället används nästa rekommenderade storlek.
- `constraints.allowedSizes` och `constraints.excludedSizes` är båda valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan bestämmas genom att [listan med tillgängliga storlekar för virtuella datorer anropas för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Alla storlekar som inte anges i `constraints.allowedSizes` är dolda och alla storlekar som inte anges i `constraints.excludedSizes` visas.
- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa maskin varu kostnaderna för de virtuella datorerna.
- `imageReference` utelämnas för avbildningar från första part, men tillhandahålls för avbildningar från tredje part. Den används för att fastställa program varu kostnaderna för de virtuella datorerna.
- `count` används för att ange lämplig multiplikator för elementet. Det har stöd för ett statiskt värde, till exempel **2**, eller ett dynamiskt värde från ett annat element, till exempel `[steps('step1').vmCount]`. Standardvärdet är **1**.
- @No__t-0 kan vara 1, 2 eller 3.
- @No__t-0 är som standard **falskt**. Filtret disk typ gör att användaren kan se alla disk typer eller endast SSD.

## <a name="sample-output"></a>Exempel på utdata
```json
"Standard_D1"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
