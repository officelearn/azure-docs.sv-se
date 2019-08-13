---
title: Avancerad läsare för iOS SDK-referens
titlesuffix: Azure Cognitive Services
description: Referens för iOS SDK för avancerad läsare
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: 47c2d772a3428594c84d3fe5e18262b5e5ab86f4
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951017"
---
# <a name="immersive-reader-sdk-reference"></a>Avancerad läsare SDK-referens

Den fördjupade läsaren för iOS SDK är en SWIFT-CocoaPod som gör att du kan integrera den fördjupade läsaren i iOS-programmet.

## <a name="functions"></a>Funktioner

SDK: `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`n visar en enskild funktion.

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
| `onFailure` | (_ fel: [Fel](#error)) -> void | En avslutning som anropas när den fördjupade läsaren inte kan läsas in. Den här stängningen [`Error`](#error) returnerar ett objekt som representerar en felkod och ett fel meddelande som är kopplat till felet. Mer information finns i [fel koderna](#error-codes). |

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

| Kod | Beskrivning |
| ---- | ----------- |
| BadArgument | Det angivna argumentet är ogiltigt. `message` mer information finns i. |
| Timeout | Det gick inte att läsa in den fördjupade läsaren inom den angivna tids gränsen. |
| TokenExpired | Angiven token har upphört att gälla. |
| Begränsas | Anrops frekvens gränsen har överskridits. |
| InternalError | Ett internt fel inträffade i den fördjupade Smartkortsläsarens visnings kontroll. Mer `message` information finns i.|

## <a name="os-version-support"></a>OS-versions stöd

Den fördjupade läsaren för iOS SDK stöds för iOS 9,0 eller högre, på iPad och iPhone.

## <a name="next-steps"></a>Nästa steg

* Utforska [iOS SDK för avancerad läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk/iOS)
* [Snabbstart: Skapa en iOS-app som startar den fördjupade läsaren (SWIFT)](./ios-quickstart.md)