---
title: Azure skapa UI-element för definition | Microsoft Docs
description: Beskriver de element som ska användas när UI-definitioner för Azure-portalen.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 41a583a77f85bb1524112fa20d9098e18bc4f431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587946"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition element
Den här artikeln beskriver schemat och egenskaperna för alla element i en CreateUiDefinition som stöds. 

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

| Egenskap  | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| namn | Ja | En intern identifierare för att referera till en specifik instans av ett element. Den vanligaste användningen av elementnamnet är i `outputs`, där utdata värdena för de angivna elementen mappas till parametrarna i mallen. Du kan också använda den för att binda värdet av ett element i den `defaultValue` av ett annat element. |
| typ | Ja | UI-kontroll att återge för elementet. En lista över typer som stöds finns i [element](#elements). |
| etikett | Ja | Texten som visas för elementet. Vissa elementtyper av innehålla flera etiketter, så att värdet kan vara ett objekt som innehåller flera strängar. |
| Standardvärde | Nej | Standardvärdet för elementet. Vissa elementtyper stöder komplexa standardvärden, så att värdet kan vara ett objekt. |
| Knappbeskrivning | Nej | Texten som visas i beskrivningen av elementet. Liknar `label`, vissa element stöd för flera verktyget tips strängar. Infogade länkar kan vara inbäddad med Markdown-syntax.
| Begränsningar | Nej | En eller flera egenskaper som används för att anpassa valideringsbeteendet elementet. Egenskaperna som stöds för begränsningar varierar elementtyp. Vissa elementtyper gör inte stöd för anpassning av valideringsbeteendet och därför har ingen egenskap med begränsningar. |
| alternativ | Nej | Ytterligare egenskaper som anpassar elementet. Liknar `constraints`, egenskaper som stöds varierar beroende på typ prvku. |
| Synlig | Nej | Anger om elementet ska visas. Om `true`, element och tillhörande underordnade element visas. Standardvärdet är `true`. Använd [logiska funktioner](create-uidefinition-functions.md#logical-functions) till dynamiskt Kontrollera värdet för den här egenskapen.

## <a name="elements"></a>Element

I dokumentationen för varje element innehåller ett exempel på UI, schema, anmärkning på beteendet för elementet (vanligtvis om verifiering och stöds anpassning) och exempel på utdata.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Nästa steg
En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
