---
title: Azure FileUpload UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.FileUpload UI-element för Azure-portalen.
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
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2886dbafe6bf20718f4e3cd2976764fc432dbb04
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021760"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI-element
En kontroll som gör att användaren kan ange en eller flera filer som ska överföras.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- `constraints.accept` Anger vilka typer av filer som visas i webbläsarens fildialogruta. Se den [HTML5-specifikationen](http://www.w3.org/TR/html5/forms.html#attr-input-accept) för tillåtna värden. Standardvärdet är **null**.
- Om `options.multiple` är inställd på **SANT**, användaren tillåts att välja fler än en fil i webbläsarens fildialogruta. Standardvärdet är **FALSKT**.
- Det här elementet har stöd för utgående filer i två lägen för baserat på värdet för `options.uploadMode`. Om **filen** anges utdata har innehållet i filen som en blob. Om **url** har angetts, och sedan filen har överförts till en tillfällig plats och utdata har blobens URL. Tillfällig blobbar ska rensas efter 24 timmar. Standardvärdet är **filen**.
- En uppladdade filen är skyddad. Utdata-URL: en innehåller en [SAS-token](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att komma åt filen under distributionen.
- Värdet för `options.openMode` avgör hur filen läses. Om filen förväntas vara oformaterad text, ange **text**; annat, ange **binära**. Standardvärdet är **text**.
- Om `options.uploadMode` är inställd på **filen** och `options.openMode` är inställd på **binära**, utdata är base64-kodad.
- `options.encoding` Anger den kodning som ska användas vid läsning av filen. Standardvärdet är **UTF-8**, och används endast när `options.openMode` är inställd på **text**.

## <a name="sample-output"></a>Exempel på utdata
Om options.multiple är FALSKT och options.uploadMode är fil, har utdata filens innehåll som en JSON-sträng:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Om options.multiple är SANT and'options.uploadMode är fil och sedan utdata har innehållet i filerna som en JSON-matris:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Om options.multiple är FALSKT och options.uploadMode är URL: en, har utdata en URL som en JSON-sträng:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Om options.multiple är SANT och options.uploadMode är URL: en, har utdata en lista över webbadresser som en JSON-matris:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

När du testar en CreateUiDefinition trunkera vissa webbläsare (till exempel Google Chrome) URL: er som genereras av Microsoft.Common.FileUpload-elementet i webbläsarens konsol. Du kan behöva högerklickar du på enskilda länkar för att kopiera de fullständiga URL: er.


## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).
