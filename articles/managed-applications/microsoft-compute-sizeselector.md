---
title: Azure SizeSelector UI-element | Microsoft Docs
description: Beskriver Microsoft.Compute.SizeSelector UI-element för Azure-portalen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 3966de95233f32a09d4799630632c2bb6a490d78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI-element
En kontroll för att välja en storlek för en eller flera instanser av virtuell dator.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

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
    "excludedSizes": []
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
- `recommendedSizes` ska innehålla minst en storlek. Den första rekommenderade storleken används som standard.
- Om en rekommenderad storlek inte är tillgängligt på den valda platsen storlek automatiskt att hoppas över. I stället används nästa Rekommenderad storlek.
- Oavsett storlek inte har angetts i den `constraints.allowedSizes` är dolt och valfri storlek inte har angetts i `constraints.excludedSizes` visas.
`constraints.allowedSizes` och `constraints.excludedSizes` både valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan fastställas genom att anropa [visa en lista med tillgängliga virtuella datorstorlekar för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- `osPlatform` måste anges, och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa kostnader för maskinvara för virtuella datorer.
- `imageReference` tas bort för från första part bilder, men det angivna för bilder från tredje part. Den används för att fastställa programvara kostnaderna för virtuella datorer.
- `count` används för att ange lämplig multiplikatorn för elementet. Den stöder ett statiskt värde som **2**, eller ett dynamiskt värde från ett annat element som `[steps('step1').vmCount]`. Standardvärdet är **1**.

## <a name="sample-output"></a>Exempel på utdata
```json
"Standard_D1"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).
