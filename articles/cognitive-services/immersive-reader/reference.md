---
title: Avancerad läsare SDK-referens
titleSuffix: Azure Cognitive Services
description: 'SDK: n för avancerad läsare innehåller ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt program.'
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 879834567b6905a070aada3dae2a41a672635c6c
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267247"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Referens guide för avancerad läsare SDK

SDK: n för avancerad läsare innehåller ett JavaScript-bibliotek som gör att du kan integrera den fördjupade läsaren i ditt program.

## <a name="functions"></a>Functions

SDK: n visar funktionerna:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Startar den fördjupade läsaren i en `iframe` i ditt webb program.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametrar

| Name | Typ | Description |
| ---- | ---- |------------ |
| `token` | sträng | Azure AD-autentiseringstoken. |
| `subdomain` | sträng | Den anpassade under domänen för den fördjupade läsar resursen i Azure. |
| `content` | [Innehåll](#content) | Ett objekt som innehåller det innehåll som ska visas i den fördjupade läsaren. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden för den fördjupade läsaren. Valfritt. |

### <a name="returns"></a>Returer

Returnerar en `Promise<LaunchResponse>` , som löses när den fördjupade läsaren läses in. `Promise`Matchar ett- [`LaunchResponse`](#launchresponse) objekt.

### <a name="exceptions"></a>Undantag

Den returnerade filen `Promise` avvisas med ett [`Error`](#error) objekt om den fördjupade läsaren inte kan läsas in. Mer information finns i [fel koderna](#error-codes).

## <a name="close"></a>stäng

Stänger den fördjupade läsaren.

Ett exempel på användnings fall för den här funktionen är om knappen Avsluta är dold genom inställning ```hideExitButton: true``` i [alternativ](#options). Sedan kan en annan knapp (till exempel ett mobil huvuds bakre pil) anropa den här ```close``` funktionen när den klickas.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Den här funktionen formaterar och uppdaterar dokumentets fördjupade läsa knapp element. Om ```options.elements``` har angetts återger den här funktionen knappar i ```options.elements``` . Annars kommer knapparna att återges i dokumentets element som har klassen ```immersive-reader-button``` .

Den här funktionen anropas automatiskt av SDK: n när fönstret läses in.

Se [valfria attribut](#optional-attributes) för fler åter givnings alternativ.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametrar

| Name | Typ | Beskrivning |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Alternativ för att konfigurera vissa beteenden för funktionen renderButtons. Valfritt. |

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller det innehåll som ska visas i den fördjupade läsaren.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Segment

Ett enda data segment som skickas till innehållet i den fördjupade läsaren.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Innehåller svaret från anropet till `ImmersiveReader.launchAsync` .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy Enum

En uppräkning som används för att ange principen för avancerad läsares cookie-användning. Se [alternativ](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>MIME-typer som stöds

| MIME-typ | Beskrivning |
| --------- | ----------- |
| text/ren | Oformaterad text. |
| text/html | HTML-innehåll. [Läs mer](#html-support)|
| program/mathml + XML | MathML (matematiskt Markup Language). [Läs mer](./how-to/display-math.md).
| Application/VND. openxmlformats-officedocument. WordprocessingML. Document | Dokument för Microsoft Word. docx-format.

### <a name="html-support"></a>HTML-stöd

| HTML | Innehåll som stöds |
| --------- | ----------- |
| Teckensnitts format | Fet, kursiv, understruken, kod, genomstruken, upphöjd, nedsänkt |
| Osorterade listor | Skiva, cirkel, kvadrat |
| Ordnade listor | Decimal, gemener, gemener, gemener, gemener, gemener och gemener |

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
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
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

| Kod | Beskrivning |
| ---- | ----------- |
| BadArgument | Det angivna argumentet är ogiltigt `message` . mer information finns i. |
| Tidsgräns | Det gick inte att läsa in den fördjupade läsaren inom den angivna tids gränsen. |
| TokenExpired | Angiven token har upphört att gälla. |
| Begränsas | Anrops frekvens gränsen har överskridits. |

## <a name="launching-the-immersive-reader"></a>Starta den fördjupade läsaren

SDK tillhandahåller standard formatet för knappen för att starta den fördjupade läsaren. Använd `immersive-reader-button` Class-attributet för att aktivera det här formatet. Mer information finns i [den här artikeln](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Valfria attribut

Använd följande attribut för att konfigurera utseendet och utseendet på knappen.

| Attribut | Beskrivning |
| --------- | ----------- |
| `data-button-style` | Anger formatet för knappen. Kan vara `icon` , `text` eller `iconAndText` . Standardvärdet är `icon` . |
| `data-locale` | Anger språkvarianten. Exempel: `en-US` eller `fr-FR`. Standardvärdet är engelska `en` . |
| `data-icon-px-size` | Anger storleken på ikonen i bild punkter. Standardvärdet är 20px. |

## <a name="browser-support"></a>Stöd för webbläsare

Använd de senaste versionerna av följande webbläsare för bästa möjliga upplevelse med avancerad läsare.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK för avancerad läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Snabb start: skapa en webbapp som startar den fördjupade läsaren (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
