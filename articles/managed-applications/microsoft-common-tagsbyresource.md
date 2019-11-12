---
title: Användar gränssnitts element för Azure TagsByResource
description: Beskriver elementet Microsoft. Common. TagsByResource UI för Azure Portal. Använd för att lägga till taggar till en resurs under distributionen.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933147"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Elementet Microsoft. Common. TagsByResource UI

En kontroll för att associera [taggar](../azure-resource-manager/resource-group-using-tags.md) med resurserna i en distribution.

## <a name="ui-sample"></a>UI-exempel

![List rutan Microsoft. Common.](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

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

## <a name="remarks"></a>Kommentarer

- Minst ett objekt i `resources` mat ris måste anges.
- Varje element i `resources` måste vara en fullständigt kvalificerad resurs typ. Dessa element visas i list rutan **resurs** och taggable av användaren.
- Resultatet av kontrollen är formaterat för enkel tilldelning av tagg värden i en Azure Resource Manager-mall. Ta emot kontrollens utdata i en mall med en parameter i mallen som visas i följande exempel:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  För varje resurs som kan märkas tilldelar du egenskapen Tags till parametervärdet för den resurs typen:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Använd funktionen [om](../azure-resource-manager/resource-group-template-functions-logical.md#if) när du använder tagsByResource-parametern. Du kan tilldela ett tomt objekt när inga Taggar tilldelas till den angivna resurs typen.

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
