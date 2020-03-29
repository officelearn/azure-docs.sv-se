---
title: Uppslukande läsare SDK-referens
titleSuffix: Azure Cognitive Services
description: Den uppslukande Reader SDK innehåller ett JavaScript-bibliotek som låter dig integrera Immersive Reader i ditt program.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156411"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Referensguide för för en uppslukande läsare SDK

Den uppslukande Reader SDK innehåller ett JavaScript-bibliotek som låter dig integrera Immersive Reader i ditt program.

## <a name="functions"></a>Funktioner

SDK exponerar funktionerna:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Startar Immersive Reader i `iframe` en i din webbapplikation.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `token` | sträng | Azure AD-autentiseringstoken. |
| `subdomain` | sträng | Den anpassade underdomänen för din Immersive Reader-resurs i Azure. |
| `content` | [Innehåll](#content) | Ett objekt som innehåller innehållet som ska visas i Immersive Reader. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden i Immersive Reader. Valfri. |

### <a name="returns"></a>Returnerar

Returnerar `Promise<LaunchResponse>`en , som löser när den uppslukande läsaren läses in. Den `Promise` löser till [`LaunchResponse`](#launchresponse) ett objekt.

### <a name="exceptions"></a>Undantag

Den returnerade `Promise` avvisas [`Error`](#error) med ett objekt om Immersive Reader inte kan läsas in. Mer information finns i [felkoderna](#error-codes).

## <a name="close"></a>stäng

Stänger Immersive Reader.

Ett exempel på användningsfall för den här ```hideExitButton: true``` funktionen är om stängningsknappen döljs genom att du ställer in [alternativ](#options). Sedan kan en annan knapp (till exempel en bakåtpil ```close``` för mobilhuvudet) anropa den här funktionen när du klickar på den.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Den här funktionen utformar och uppdaterar dokumentets immersive Reader-knappelement. Om ```options.elements``` det finns, kommer den ```options.elements```här funktionen att återge knappar i . Annars återges knapparna i dokumentets element som har ```immersive-reader-button```klassen .

Den här funktionen anropas automatiskt av SDK när fönstret läses in.

Se [Valfria attribut](#optional-attributes) för fler återgivningsalternativ.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Alternativ för att konfigurera vissa beteenden i funktionen renderButtons. Valfri. |

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller innehållet som ska visas i Immersive Reader.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Segment

En enda bit data, som kommer att skickas in i innehållet i den uppslukande läsaren.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Innehåller svaret från anropet till `ImmersiveReader.launchAsync`.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy uppräkning

En uppräkning som används för att ange principen för Immersive Reader cookie-användning. Se [alternativ](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>MIME-typer som stöds

| MIME-typ | Beskrivning |
| --------- | ----------- |
| text/oformaterad | Oformaterad text. |
| text/html | HTML-innehåll. [Läs mer](#html-support)|
| program/mathml+xml | Matematiskt markeringsspråk (MathML). [Läs mer](./how-to/display-math.md).
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Dokument i Microsoft Word .docx-format.

### <a name="html-support"></a>HTML-stöd

| HTML | Innehåll som stöds |
| --------- | ----------- |
| Teckenstilar | Fetstil, kursiv, understrykning, kod, genomstruken, upphöjd, nedsänkt |
| Osorterade listor | Skiva, Cirkel, Kvadrat |
| Ordnade listor | Decimal, Övre alfa, nedre alfa, övre-romerska, nedre-romerska |
| Hyperlänkar | Kommer snart |

Taggar som inte stöds kommer att återges jämförbart. Bilder och tabeller stöds för närvarande inte.

### <a name="options"></a>Alternativ

Innehåller egenskaper som konfigurerar vissa beteenden för den uppslukande läsaren.

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

Alternativ för rendering av immersive Reader-knapparna.

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
| BadArgument (onda) | Det angivna argumentet är `message` ogiltigt, se för mer information. |
| Timeout | Den uppslukande läsaren kunde inte läsas in inom den angivna tidsgränsen. |
| TokenExpired | Den angivna token har upphört att gälla. |
| Spjäll | Gränsen för samtalshastighet har överskridits. |

## <a name="launching-the-immersive-reader"></a>Starta den uppslukande läsaren

SDK ger standardformat för knappen för att starta Immersive Reader. Använd `immersive-reader-button` klassattributet för att aktivera den här formateringen. Se [den här artikeln](./how-to-customize-launch-button.md) för mer information.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Valfria attribut

Använd följande attribut för att konfigurera knappens utseende och känsla.

| Attribut | Beskrivning |
| --------- | ----------- |
| `data-button-style` | Anger knappens format. Kan `icon`vara `text`, `iconAndText`eller . Standardvärdet `icon`till . |
| `data-locale` | Ställer in språken. Exempel: `en-US` eller `fr-FR`. Standard på `en`engelska . |
| `data-icon-px-size` | Anger ikonens storlek i pixlar. Standardvärdet till 20px. |

## <a name="browser-support"></a>Stöd för webbläsare

Använd de senaste versionerna av följande webbläsare för bästa upplevelse med Immersive Reader.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Nästa steg

* Utforska [Immersive Reader SDK på GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Snabbstart: Skapa en webbapp som startar Immersive Reader (C#)](./quickstart.md)
