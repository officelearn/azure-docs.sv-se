---
title: Gränssnittselement för FileUpload
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.FileUpload för Azure portal. Gör det möjligt för användare att ladda upp filer när de distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652494"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload användargränssnittselement

En kontroll som gör att en användare kan ange en eller flera filer att ladda upp.

## <a name="ui-sample"></a>Exempel på användargränssnitt

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

## <a name="sample-output"></a>Exempel på utdata

Om options.multiple är falskt och options.uploadMode är fil, har utdata innehållet i filen som en JSON-sträng:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Om options.multiple är sant och'options.uploadMode är fil, har utdata innehållet i filerna som en JSON-matris:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Om options.multiple är falskt och options.uploadMode är url, har utdata en URL som en JSON-sträng:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Om options.multiple är sant och options.uploadMode är url, har utdata en lista med webbadresser som en JSON-matris:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

När du testar en CreateUiDefinition trunkerar vissa webbläsare (som Google Chrome) webbadresser som genereras av elementet Microsoft.Common.FileUpload i webbläsarkonsolen. Du kan behöva högerklicka på enskilda länkar för att kopiera de fullständiga webbadresserna.

## <a name="remarks"></a>Anmärkningar

- `constraints.accept`anger vilka typer av filer som visas i webbläsarens fildialogruta. Se [HTML5-specifikationen](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) för tillåtna värden. Standardvärdet är **null**.
- Om `options.multiple` värdet är **true**får användaren välja mer än en fil i webbläsarens fildialogruta. Standardvärdet är **falskt**.
- Det här elementet stöder överföring av filer `options.uploadMode`i två lägen baserat på värdet av . Om **filen** anges har utdata innehållet i filen som en blob. Om **url** anges överförs filen till en tillfällig plats och utdata har url:en för bloben. Tillfälliga blobbar rensas efter 24 timmar. Standardvärdet är **fil**.
- En uppladdad fil är skyddad. Utdata-URL:en innehåller en [SAS-token](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för åtkomst till filen under distributionen.
- Värdet för `options.openMode` avgör hur filen läsav sig. Om filen förväntas vara oformaterad text anger du **text**. annars anger du **binär .** Standardvärdet är **text**.
- Om `options.uploadMode` är **file** inställt `options.openMode` på fil och är inställt på **binära**, utdata är base64-kodade.
- `options.encoding`anger vilken kodning som ska användas när du läser filen. Standardvärdet är **UTF-8**och används `options.openMode` endast när är inställt på **text**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
