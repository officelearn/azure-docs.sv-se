---
title: Gränssnittselementet TagsByResource
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.TagsByResource för Azure portal. Används för att använda taggar på en resurs under distributionen.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652208"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource-gränssnittselement

En kontroll för att associera [taggar](../management/tag-resources.md) med resurserna i en distribution.

## <a name="ui-sample"></a>Exempel på användargränssnitt

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Anmärkningar

- Minst ett objekt `resources` i matrisen måste anges.
- Varje element `resources` i måste vara en fullständigt kvalificerad resurstyp. Dessa element visas i listrutan **Resurs** och kan taggas av användaren.
- Utdata för kontrollen är formaterad för enkel tilldelning av taggvärden i en Azure Resource Manager-mall. Om du vill ta emot kontrollens utdata i en mall tar du med en parameter i mallen som visas i följande exempel:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  För varje resurs som kan taggas tilldelar du egenskapen taggar till parametervärdet för den resurstypen:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Använd [funktionen om](../templates/template-functions-logical.md#if) när du öppnar parametern tagsByResource. Du kan tilldela ett tomt objekt när inga taggar har tilldelats den angivna resurstypen.

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
