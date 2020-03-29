---
title: Gränssnittselement för IdentitySelector
description: Beskriver användargränssnittselementet Microsoft.ManagedIdentity.IdentitySelector för Azure-portalen. Används för att tilldela hanterade identiteter till en resurs.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087549"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector UI element

En kontroll för tilldelning av [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för en resurs i en distribution.

## <a name="ui-sample"></a>Exempel på användargränssnitt

Kontrollen består av följande element:

![Microsoft.ManagedIdentity.IdentitySelector första steget](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

När användaren väljer **Lägg till**öppnas följande formulär. Användaren kan välja en eller flera användartilldelade identiteter för resursen.

![Microsoft.ManagedIdentity.IdentitySelector andra steget](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

De valda identiteterna visas i tabellen. Användaren kan lägga till eller ta bort objekt från den här tabellen.

![Microsoft.ManagedIdentity.IdentitySelector tredje steget](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>Schema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Anmärkningar

- Använd **defaultValue.systemAssignedIdentity** om du vill ange ett initialt värde för kontrollen systemtilldelade identitetsalternativ. Standardvärdet är **Av**. Följande värden är tillåtna:
  - **På** – Ett system tilldelat identitet tilldelas resursen.
  - **Av** – Ett system som tilldelats identitet tilldelas inte resursen.
  - **OnOnly** – En tilldelad identitet tilldelas resursen. Användare kan inte redigera det här värdet under distributionen.
  - **OffOnly** – Ett system som tilldelats identitet tilldelas inte till resursen. Användare kan inte redigera det här värdet under distributionen.

- Om **options.hideSystemAssignedIdentity** är inställt på **true**visas inte användargränssnittet för att konfigurera den tilldelade datorn. Standardvärdet för det här alternativet är **falskt**.
- Om **options.hideUserAssignedIdentity** är inställt på **true**visas inte användargränssnittet för att konfigurera den tilldelade användarens identitet. Resursen tilldelas inte en användartilldelad identitet. Standardvärdet för det här alternativet är **falskt**.

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).