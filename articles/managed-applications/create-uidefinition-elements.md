---
title: Azure-hanterade program skapa UI definition funktioner | Microsoft Docs
description: "Beskriver funktionerna som ska användas när man skapar UI definitioner för hanterade program i Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 23e407bf93bc51116ca45339bffcb801d69290f0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition element
Den här artikeln beskriver schemat och egenskaperna för alla element som stöds av en CreateUiDefinition. Du använder de här elementen när [att skapa ett Azure hanterade program](publish-service-catalog-app.md). Schemat för de flesta element är följande:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| namn | Ja | En intern identifierare för att referera till en specifik instans av ett element. Den vanligaste användningen av elementnamnet är i `outputs`, där utdata värdena i de angivna elementen mappas till parametrarna för mallen. Du kan också använda den för att binda värdet av ett element i den `defaultValue` av ett annat element. |
| typ | Ja | UI-kontrollen ska renderas för elementet. En lista över typer som stöds, se [element](#elements). |
| Etikett | Ja | Texten som visas för elementet. Vissa elementtyper av innehålla flera etiketter, så värdet kan vara ett objekt som innehåller flera strängar. |
| Standardvärde | Nej | Standardvärdet för elementet. Vissa elementtyper har stöd för komplexa standardvärden så värdet kan vara ett objekt. |
| Knappbeskrivning | Nej | Texten som visas i beskrivningen av elementet. Liknar `label`, vissa element stöd för flera verktyget tips strängar. Infogade länkar inbäddade med markdownsyntax.
| Villkor | Nej | En eller flera egenskaper som används för att anpassa valideringsbeteendet för elementet. Egenskaperna som stöds för begränsningar varierar beroende på elementtyp. Vissa elementtyper av gör inte stöd för anpassning av funktionen för validering och därför har ingen egenskap med begränsningar. |
| alternativ | Nej | Ytterligare egenskaper som anpassar för elementet. Liknar `constraints`, egenskaper som stöds beror på elementtypen. |
| Synliga | Nej | Anger om elementet är visas. Om `true`, element och tillhörande underordnade element visas. Standardvärdet är `true`. Använd [logiska funktioner](create-uidefinition-functions.md#logical-functions) till dynamiskt Kontrollera värdet för den här egenskapen.

## <a name="elements"></a>Element

I dokumentationen för varje element innehåller ett exempel på UI, schemat, anmärkning på beteendet för element (vanligtvis om verifiering och anpassning som stöds) och exempel på utdata.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Nästa steg
* En introduktion till hanterade program, se [översikt över Azure Managed Application](overview.md).
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
