---
title: Anpassa Avancerad läsare-knappen
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du anpassar knappen som startar den fördjupade läsaren.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: b24a9dcb7a369708b1374f367186dc4d9a6ce475
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146883"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Anpassa knappen för avancerad läsare

Den här artikeln visar hur du anpassar knappen som startar den fördjupade läsaren så att den passar dina programs behov.

## <a name="add-the-immersive-reader-button"></a>Lägg till knappen för avancerad läsare

SDK för avancerad läsare tillhandahåller standardformat för knappen som startar den fördjupade läsaren. Använd `immersive-reader-button` Class-attributet för att aktivera det här formatet.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Anpassa knapp formatet

Använd `data-button-style` attributet för att ange formatet för knappen. De tillåtna värdena är `icon` , `text` och `iconAndText` . Standardvärdet är `icon`.

### <a name="icon-button"></a>Ikon knapp

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Detta återger följande:

![Det här är knappen renderad text](./media/button-icon.png)

### <a name="text-button"></a>Text knapp

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Detta återger följande:

![Det här är knappen renderad avancerad läsare.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Knapp för ikon och text

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Detta återger följande:

![Ikon knapp](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Anpassa knapp texten

Konfigurera språket och alternativ texten för knappen med hjälp av- `data-locale` attributet. Standardspråk är engelska.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Anpassa ikonens storlek

Storleken på ikonen för avancerad läsare kan konfigureras med hjälp av- `data-icon-px-size` attributet. Detta anger storleken på ikonen i bild punkter. Standard storleken är 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)