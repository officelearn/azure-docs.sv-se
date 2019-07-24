---
title: Avancerad läsare SDK-referens
titleSuffix: Azure Cognitive Services
description: Referens för SDK för avancerad läsare
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 67da7d67a3165583a872c2b435c3cdca9763d4dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443810"
---
# <a name="immersive-reader-sdk-reference"></a>Avancerad läsare SDK-referens

SDK för avancerad läsare är ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt webb program.

## <a name="functions"></a>Funktioner

SDK: `ImmersiveReader.launchAsync(token, subdomain, content, options)`n visar en enskild funktion.

### <a name="launchasync"></a>launchAsync

Startar den fördjupade läsaren `iframe` i en i ditt webb program.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `token` | sträng | Azure AD-autentiseringstoken. Se [själv studie kursen om Azure AD-autentisering](./azure-active-directory-authentication.md). |
| `subdomain` | sträng | Den anpassade under domänen för den fördjupade läsar resursen i Azure. Se [Azure AD-autentiseringen How-to](./azure-active-directory-authentication.md). |
| `content` | [Innehåll](#content) | Ett objekt som innehåller det innehåll som ska visas i den fördjupade läsaren. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden för den fördjupade läsaren. Valfri. |

#### <a name="returns"></a>Returnerar

Returnerar en `Promise<HTMLDivElement>` som matchar när den fördjupade läsaren läses in. Matchar ett `div` element vars enda underordnade är ett `iframe` element som innehåller den fördjupade läsar sidan. `Promise`

#### <a name="exceptions"></a>Undantag

Den returnerade `Promise` filen avvisas [`Error`](#error) med ett objekt om den fördjupade läsaren inte kan läsas in. Mer information finns i [fel koderna](#error-codes).

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller det innehåll som ska visas i den fördjupade läsaren.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>MIME-typer som stöds

| MIME-typ | Beskrivning |
| --------- | ----------- |
| text/ren | Oformaterad text. |
| program/mathml + XML | MathML (matematiskt Markup Language). [Läs mer](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Alternativ

Innehåller egenskaper som konfigurerar vissa beteenden för den fördjupade läsaren.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Fel

Innehåller information om felet.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Felkoder

| Kod | Beskrivning |
| ---- | ----------- |
| BadArgument | Det angivna argumentet är ogiltigt. `message` mer information finns i. |
| Timeout | Det gick inte att läsa in den fördjupade läsaren inom den angivna tids gränsen. |
| TokenExpired| Angiven token har upphört att gälla. |

## <a name="launching-the-immersive-reader"></a>Starta den fördjupade läsaren

SDK tillhandahåller standard formatet för knappen för att starta den fördjupade läsaren. `immersive-reader-button` Använd Class-attributet för att aktivera det här formatet.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Valfria attribut

Använd följande attribut för att konfigurera utseendet och utseendet på knappen.

| Attribut | Beskrivning |
| --------- | ----------- |
| `data-button-style` | Anger formatet för knappen. Kan vara `icon`, `text`eller. `iconAndText` Som standard `icon`. |
| `data-locale` | Anger språkvarianten, t `en-US`. `fr-FR`ex. Standardvärdet är engelska. |
| `data-icon-px-size` | Anger storleken på ikonen i bild punkter. Standardvärdet är 20px. |

## <a name="browser-support"></a>Webb läsar stöd

Använd de senaste versionerna av följande webbläsare för bästa möjliga upplevelse med avancerad läsare.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK för avancerad läsare på GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Snabbstart: Skapa en webbapp som startar den fördjupade läsarenC#()](./quickstart.md)