---
title: Anpassa knappen Immersive Reader
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du anpassar knappen som startar Immersive Reader.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946214"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Så här anpassar du knappen Uppslukande läsare

Den här artikeln visar hur du anpassar knappen som startar Immersive Reader för att passa behoven i ditt program.

## <a name="add-the-immersive-reader-button"></a>Lägg till knappen Uppslukande läsare

Den uppslukande Reader SDK ger standard styling för knappen som startar Immersive Reader. Använd `immersive-reader-button` klassattributet för att aktivera den här formateringen.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Anpassa knappformatet

Använd `data-button-style` attributet för att ange knappens format. De tillåtna `icon` `text`värdena `iconAndText`är , och . Standardvärdet är `icon`.

### <a name="icon-button"></a>Knappen Ikon

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Detta gör följande:

![Knappen Ikon](./media/button-icon.png)

### <a name="text-button"></a>Knappen Text

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Detta gör följande:

![Knappen Ikon](./media/button-text.png)

### <a name="icon-and-text-button"></a>Knappen Ikon och text

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Detta gör följande:

![Knappen Ikon](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Anpassa knapptexten

Konfigurera språket och alternativtexten för `data-locale` knappen med hjälp av attributet. Standardspråk är engelska.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Anpassa ikonens storlek

Storleken på ikonen Immersive Reader kan konfigureras med `data-icon-px-size` attributet. Detta anger ikonens storlek i pixlar. Standardstorleken är 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsar-SDK-referensen](./reference.md)