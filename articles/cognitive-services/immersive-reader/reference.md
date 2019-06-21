---
title: Uppslukande läsare SDK-referens
titlesuffix: Azure Cognitive Services
description: Referens för uppslukande läsaren SDK
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: bf337f8d4eac859dc39a36ba12dd9645aa23fe41
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297045"
---
# <a name="immersive-reader-sdk-reference"></a>Uppslukande läsare SDK-referens

Uppslukande läsare SDK är ett JavaScript-bibliotek som gör det möjligt att integrera uppslukande läsaren i ditt webbprogram.

## <a name="functions"></a>Functions

SDK: N Exponerar en enda funktion `ImmersiveReader.launchAsync(token, data, options)`.

### <a name="launchasync"></a>launchAsync

Startar uppslukande läsaren inom en `iframe` i ditt webbprogram.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `token` | string | Åtkomsttoken som anskaffats vid anrop till den `issueToken` slutpunkt. |
| `resourceName` | string | Reserverad. Måste anges till `null`. |
| `content` | [Innehåll](#content) | Ett objekt som innehåller innehållet som ska visas i uppslukande läsaren. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden av uppslukande läsaren. Valfri. |

#### <a name="returns"></a>Returnerar

Returnerar en `Promise<HTMLDivElement>` som löser när uppslukande läsaren har lästs in. Den `Promise` motsvarar en `div` element vars endast underordnade är en `iframe` element som innehåller sidan uppslukande läsare.

#### <a name="exceptions"></a>Undantag

Den returnerade `Promise` avvisas med en [ `Error` ](#error) objekt om uppslukande läsaren inte kan läsas in. Mer information finns i den [felkoder](#error-codes).

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller innehållet som ska visas i uppslukande läsaren.

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
| text/plain | Oformaterad text. |
| program/mathml + xml | Matematiska MathML (Markup Language). [Läs mer](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Alternativ

Innehåller egenskaper som konfigurerar beteendena hos den uppslukande läsaren.

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
| BadArgument | Angivna argumentet är ogiltigt, finns i `message` mer information. |
| Timeout | Det gick inte att läsa in inom den angivna tidsgränsen uppslukande läsaren. |
| TokenExpired| Den angivna token har upphört att gälla. |

## <a name="launching-the-immersive-reader"></a>Starta uppslukande läsaren

SDK innehåller standard stilen för knappen för att starta uppslukande läsaren. Använd den `immersive-reader-button` klassattribut att aktivera den här formatering.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Valfria attribut

Använd följande attribut för att konfigurera utseendet och känslan av knappen.

| Attribut | Beskrivning |
| --------- | ----------- |
| `data-button-style` | Anger formatet för knappen. Kan vara `icon`, `text`, eller `iconAndText`. Som standard `icon`. |
| `data-locale` | Anger språk, t.ex. `en-US`, `fr-FR`. Standardvärdet är engelska. |
| `data-icon-px-size` | Anger storleken på ikonen i bildpunkter. Standardvärdet är 20px. |

## <a name="browser-support"></a>Webbläsarstöd

Använd de senaste versionerna av följande webbläsare för bästa möjliga upplevelse med uppslukande läsaren.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Nästa steg

* Utforska den [uppslukande läsare SDK på GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Snabbstart: Skapa en webbapp som startar uppslukande läsaren (C#)](./quickstart.md)