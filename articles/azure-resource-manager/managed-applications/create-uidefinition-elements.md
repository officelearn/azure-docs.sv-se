---
title: Skapa ui definition element
description: Beskriver de element som ska användas när du skapar gränssnittsdefinitioner för Azure-portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086700"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition-element

I den här artikeln beskrivs schemat och egenskaperna för alla element som stöds i en CreateUiDefinition. 

## <a name="schema"></a>Schema

Schemat för de flesta element är följande:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| namn | Ja | En intern identifierare som refererar till en viss instans av ett element. Den vanligaste användningen av elementnamnet `outputs`finns i , där utdatavärdena för de angivna elementen mappas till mallens parametrar. Du kan också använda den för att binda `defaultValue` utdatavärdet för ett element till ett annat element. |
| typ | Ja | Den gränssnittskontroll som ska återges för elementet. En lista över typer som stöds finns i [Elements](#elements). |
| etikett | Ja | Elementets visningstext. Vissa elementtyper innehåller flera etiketter, så värdet kan vara ett objekt som innehåller flera strängar. |
| Standardvärde | Inga | Standardvärdet för elementet. Vissa elementtyper stöder komplexa standardvärden, så värdet kan vara ett objekt. |
| Tooltip | Inga | Texten som ska visas i elementets verktygsspets. På `label`samma sätt som stöder vissa element flera verktygstipssträngar. Infogade länkar kan bäddas in med marksyntaxen.
| Begränsningar | Inga | En eller flera egenskaper som används för att anpassa verifieringsbeteendet för elementet. Egenskaperna som stöds för begränsningar varierar beroende på elementtyp. Vissa elementtyper stöder inte anpassning av valideringsbeteendet och har därför ingen egenskap för begränsningar. |
| alternativ | Inga | Ytterligare egenskaper som anpassar elementets beteende. I `constraints`likhet med , de egenskaper som stöds varierar beroende på elementtyp. |
| Synliga | Inga | Anger om elementet visas. Om `true`visas elementet och tillämpliga underordnade element. Standardvärdet är `true`. Använd [logiska funktioner](create-uidefinition-functions.md#logical-functions) för att dynamiskt styra egenskapens värde.

## <a name="elements"></a>Element

Dokumentationen för varje element innehåller ett gränssnittsexempel, schema, anmärkningar om elementets beteende (vanligtvis om validering och anpassning som stöds) och exempelutdata.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.identityselector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Nästa steg

En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
