---
title: Avancerad läsare för iOS SDK-referens
titleSuffix: Azure Cognitive Services
description: Den fördjupade läsaren för iOS SDK är en SWIFT-CocoaPod som gör att du kan integrera den fördjupade läsaren i iOS-programmet.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 67d6b8c22c5635bd789078a7f91b02f8b07e5e70
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903131"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>Avancerad läsare SDK-referens för iOS

Den fördjupade läsaren för iOS SDK är en SWIFT-CocoaPod som gör att du kan integrera den fördjupade läsaren i iOS-programmet.

## <a name="functions"></a>Funktioner

SDK visar en enskild funktion `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Startar den fördjupade läsaren genom att starta en Visa kontrollant i iOS-programmet.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Parametrar

| Namn | Typ | Beskrivning |
| ---- | ---- |------------ |
| `navController` | UINavigationController | Navigerings styrenheten för iOS-programmet som funktionen anropas från. |
| `token` | Sträng | Azure AD-autentiseringstoken. Se [Azure AD-autentiseringen How-to](./azure-active-directory-authentication.md). |
| `subdomain` | Sträng | Den anpassade under domänen för den fördjupade läsar resursen i Azure. Se [Azure AD-autentiseringen How-to](./azure-active-directory-authentication.md). |
| `content` | [Innehåll](#content) | Ett objekt som innehåller det innehåll som ska visas i den fördjupade läsaren. |
| `options` | [Alternativ](#options) | Alternativ för att konfigurera vissa beteenden för den fördjupade läsaren. Valfri. |
| `onSuccess` | ()-> void | En avslutning som anropas när den fördjupade läsaren har startat. |
| `onFailure` | (_ fel: [fel](#error))-> void | En avslutning som anropas när den fördjupade läsaren inte kan läsas in. Den här stängningen returnerar ett [`Error`](#error) -objekt som representerar en felkod och ett fel meddelande som är kopplat till felet. Mer information finns i [fel koderna](#error-codes). |

## <a name="types"></a>Typer

### <a name="content"></a>Innehåll

Innehåller det innehåll som ska visas i den fördjupade läsaren.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>MIME-typer som stöds

| MIME-typ | Beskrivning |
| --------- | ----------- |
| text/ren | Oformaterad text. |
| program/mathml + XML | MathML (matematiskt Markup Language). [Läs mer](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Alternativ

Innehåller egenskaper som konfigurerar vissa beteenden för den fördjupade läsaren.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Fel

Innehåller information om felet.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Felkoder

| Programmera | Beskrivning |
| ---- | ----------- |
| BadArgument | Det angivna argumentet är ogiltigt, se `message` för mer information. |
| Standardvärde | Det gick inte att läsa in den fördjupade läsaren inom den angivna tids gränsen. |
| TokenExpired | Angiven token har upphört att gälla. |
| Begränsas | Anrops frekvens gränsen har överskridits. |
| InternalError | Ett internt fel inträffade i den fördjupade Smartkortsläsarens visnings kontroll. Se `message` för mer information.|

## <a name="os-version-support"></a>OS-versions stöd

Den fördjupade läsaren för iOS SDK stöds för iOS 9,0 eller högre, på iPad och iPhone.

## <a name="next-steps"></a>Nästa steg

* Utforska [iOS SDK för avancerad läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Snabb start: skapa en iOS-app som startar den fördjupade läsaren (SWIFT)](./ios-quickstart.md)