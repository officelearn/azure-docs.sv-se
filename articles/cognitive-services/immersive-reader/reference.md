---
title: Avancerad läsare SDK-referens
titleSuffix: Azure Cognitive Services
description: SDK för avancerad läsare är ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt webb program.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 09244b634fa2603a7dc92af3c78d171f8d6bd9df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903113"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Referens guide för avancerad läsare SDK

SDK för avancerad läsare är ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt webb program.

## <a name="functions"></a>Funktioner

SDK: n visar funktionerna:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Startar den fördjupade läsaren i en `iframe` i ditt webb program.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `token` | sträng | Azure AD-autentiseringstoken. Se [Azure AD-autentiseringen How-to](./azure-active-directory-authentication.md). |
| `subdomain` | sträng | Den anpassade under domänen för den fördjupade läsar resursen i Azure. Se [Azure AD-autentiseringen How-to](./azure-active-directory-authentication.md). |
| `content` | [Innehåll](#content) | Ett objekt som innehåller det innehåll som ska visas i den fördjupade läsaren. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden för den fördjupade läsaren. Valfri. |

### <a name="returns"></a>Avkastning

Returnerar ett `Promise<HTMLDivElement>`som löses när den fördjupade läsaren läses in. `Promise` matchar ett `div`-element vars enda underordnade är ett `iframe`-element som innehåller fördjupad läsar sidan.

### <a name="exceptions"></a>Undantag

Den returnerade `Promise` avvisas med ett [`Error`](#error) -objekt om den fördjupade läsaren inte kan läsas in. Mer information finns i [fel koderna](#error-codes).

## <a name="close"></a>stäng

Stänger den fördjupade läsaren.

Ett exempel på användnings fall är om knappen Avsluta är dold genom att ange ```hideExitButton: true``` i [alternativ](#options). Sedan kan en annan knapp (till exempel ett mobil huvuds bakre pil) anropa den här ```close``` funktionen när den klickas.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Den här funktionen formaterar och uppdaterar dokumentets fördjupade läsa knapp element. Om ```options.elements``` anges återger den här funktionen knappar i ```options.elements```. Annars återges knapparna i dokumentets element som har klassen ```immersive-reader-button```.

Den här funktionen anropas automatiskt av SDK: n när fönstret läses in.

Se [valfria attribut](#optional-attributes) för fler åter givnings alternativ.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Alternativ för att konfigurera vissa beteenden för funktionen renderButtons. Valfri. |

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller det innehåll som ska visas i den fördjupade läsaren.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Omdirigeringstabell

Ett enda data segment som skickas till innehållet i den fördjupade läsaren.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>MIME-typer som stöds

| MIME-typ | Beskrivning |
| --------- | ----------- |
| text/ren | Oformaterad text. |
| text/html | HTML-innehåll. [Läs mer](#html-support)|
| program/mathml + XML | MathML (matematiskt Markup Language). [Läs mer](https://developer.mozilla.org/en-US/docs/Web/MathML).
| Application/VND. openxmlformats-officedocument. WordprocessingML. Document | Dokument för Microsoft Word. docx-format.

### <a name="html-support"></a>HTML-stöd
| HTML | Innehåll som stöds |
| --------- | ----------- |
| Teckensnitts format | Fet, kursiv, understruken, kod, genomstruken, upphöjd, nedsänkt |
| Osorterade listor | Skiva, cirkel, kvadrat |
| Ordnade listor | Decimal, gemener, gemener, gemener, gemener, gemener och gemener |
| Hyperlänkar | Kommer snart |

Taggar som inte stöds kommer att återges jämförbart. Bilder och tabeller stöds inte för närvarande.

### <a name="options"></a>Alternativ

Innehåller egenskaper som konfigurerar vissa beteenden för den fördjupade läsaren.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Alternativ för att återge knapparna för avancerad läsare.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
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

| Programmera | Beskrivning |
| ---- | ----------- |
| BadArgument | Det angivna argumentet är ogiltigt, se `message` för mer information. |
| Standardvärde | Det gick inte att läsa in den fördjupade läsaren inom den angivna tids gränsen. |
| TokenExpired | Angiven token har upphört att gälla. |
| Begränsas | Anrops frekvens gränsen har överskridits. |

## <a name="launching-the-immersive-reader"></a>Starta den fördjupade läsaren

SDK tillhandahåller standard formatet för knappen för att starta den fördjupade läsaren. Använd attributet `immersive-reader-button` Class för att aktivera den här formateringen.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Valfria attribut

Använd följande attribut för att konfigurera utseendet och utseendet på knappen.

| Attribut | Beskrivning |
| --------- | ----------- |
| `data-button-style` | Anger formatet för knappen. Kan vara `icon`, `text`eller `iconAndText`. Standardvärdet är `icon`. |
| `data-locale` | Anger språkvarianten. Exempel: `en-US` eller `fr-FR`. Standardvärdet är engelska `en`. |
| `data-icon-px-size` | Anger storleken på ikonen i bild punkter. Standardvärdet är 20px. |

## <a name="browser-support"></a>Webb läsar stöd

Använd de senaste versionerna av följande webbläsare för bästa möjliga upplevelse med avancerad läsare.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK för avancerad läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Snabb start: skapa en webbapp som startar den fördjupadeC#läsaren ()](./quickstart.md)
