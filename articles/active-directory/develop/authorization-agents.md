---
title: Tillståndsombud och hur man aktiverar dem | Azure
description: Lär dig mer om de olika auktoriseringsagenter som Microsoft Authentication Library (MSAL) gör att din Android-app kan använda och hur du aktiverar dem.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085333"
---
# <a name="authorization-agents-android"></a>Auktoriseringsagenter (Android)

I den här artikeln beskrivs de olika auktoriseringsagenter som med MSAL (Microsoft Authentication Library) tillåter att appen kan använda och hur du aktiverar den.

Att välja en specifik strategi för auktoriseringsagenter är valfritt och representerar ytterligare funktioner som appar kan anpassa. De flesta appar använder STANDARDINSTÄLLNINGARNA FÖR MSAL (se [Förstå konfigurationsfilen](msal-configuration.md) för Android MSAL för att se de olika standardinställningarna).

MSAL stöder auktorisering med hjälp av en `WebView`eller systemwebbläsaren.  Bilden nedan visar hur det `WebView`ser ut med hjälp av , eller systembläddraren med CustomTabs eller utan CustomTabs:

![EXEMPEL PÅ MSAL-inloggning](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Enkla inloggningskonsekvenser

Som standard använder program som är integrerade med MSAL systembläddrarens anpassade flikar för att auktorisera. Till skillnad från WebViews delar anpassade flikar en cookie-burk med standardsystembläddraren som möjliggör färre inloggningar med webb eller andra inbyggda appar som har integrerats med anpassade flikar.

Om programmet använder `WebView` en strategi utan att integrera Microsoft Authenticator- eller Company Portal-stöd i sin app har användarna inte en SSO-upplevelse (Single Sign On) på enheten eller mellan inbyggda appar och webbappar.

Om programmet använder MSAL med Stöd för Microsoft Authenticator eller Company Portal kan användarna ha en SSO-upplevelse över program om användaren har en aktiv inloggning med en av apparna.

## <a name="webview"></a>Webview

Om du vill använda WebView i appen placerar du följande rad i appkonfigurationen JSON som skickas till MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

När du använder `WebView`appen loggar användaren in direkt till appen. Tokens hålls inne i appens sandlåda och är inte tillgängliga utanför appens cookieburk. Det kan leda till att användaren inte kan ha en SSO-upplevelse över program om inte apparna integreras med Authenticator eller Company Portal.

Ger `WebView` dock möjlighet att anpassa utseendet och känslan för inloggningsgränssnittet. Mer information om hur du gör den här anpassningen finns i [Android WebViews.](https://developer.android.com/reference/android/webkit/WebView)

## <a name="default-browser-plus-custom-tabs"></a>Standardwebbläsare plus anpassade flikar

Som standard använder MSAL webbläsaren och en [anpassad flikstrategi.](https://developer.chrome.com/multidevice/android/customtabs) Du kan uttryckligen ange den här strategin `DEFAULT` för att förhindra ändringar i framtida versioner till genom att använda följande JSON-konfiguration i den anpassade konfigurationsfilen:

```json
"authorization_user_agent" : "BROWSER"
```

Använd den här metoden för att ge en SSO-upplevelse via enhetens webbläsare. MSAL använder en delad cookie-burk, som gör att andra inbyggda appar eller webbappar kan uppnå SSO på enheten med hjälp av den kvarstående sessionscookien som ställts in av MSAL.

## <a name="browser-selection-heuristic"></a>Webbläsare val heuristisk

Eftersom det är omöjligt för MSAL att ange det exakta webbläsarpaketet som ska användas på var och en av de breda utbudet av Android-telefoner, implementerar MSAL en webbläsare val heuristisk som försöker ge den bästa cross-device SSO.

MSAL hämtar den fullständiga listan över webbläsare som är installerade på enheten för att välja vilken webbläsare som ska användas. Listan är i den ordning som returneras av pakethanteraren, vilket indirekt återspeglar användarens preferenser. Standardwebbläsaren är till exempel den första posten i listan om den är den första posten. Den _första_ webbläsaren i listan kommer att väljas oavsett om den stöder anpassade flikar. Om webbläsaren stöder anpassade flikar startar MSAL fliken Anpassad. Anpassade flikar ser ut och `WebView` känner sig närmare en i-app och tillåter grundläggande anpassning av användargränssnittet. Se [Anpassade flikar i Android](https://developer.chrome.com/multidevice/android/customtabs) om du vill veta mer.

Om det inte finns några webbläsarpaket på enheten använder `WebView`MSAL i appen .

Ordningen på webbläsarna i webbläsarlistan bestäms av operativsystemet. Det är i ordning från de flesta föredragna till minst. Om enhetens standardinställning inte ändras bör samma webbläsare startas för varje inloggning för att säkerställa en SSO-upplevelse.

> [!NOTE]
> MSAL föredrar inte längre alltid Chrome om en annan webbläsare är inställd som standard. På en enhet som har både Chrome och en annan webbläsare förinstallerad använder MSAL till exempel den webbläsare som användaren har angett som standard.

### <a name="tested-browsers"></a>Testade webbläsare

Följande webbläsare har testats för att se om `"redirect_uri"` de omdirigeras till den angivna i konfigurationsfilen på rätt sätt:

| | Inbyggd webbläsare | Chrome | Opera  | Microsoft Edge | UC Webbläsare | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Passera | Passera |ej tillämpligt |ej tillämpligt |ej tillämpligt |ej tillämpligt |
| Samsung S7 (API 25) | passera* | Passera | Passera | Passera | Misslyckas |Passera |
| Huawei (API 26) |passera** | Passera | Misslyckas | Passera | Passera |Passera |
| Vivo (API 26) |Passera|Passera|Passera|Passera|Passera|Misslyckas|
| Pixel 2 (API 26) |Passera | Passera | Passera | Passera | Misslyckas |Passera |
| Oppo | Passera | ej tillämplig*** |ej tillämpligt  |ej tillämpligt |ej tillämpligt | ej tillämpligt|
| OnePlus (API 25) |Passera | Passera | Passera | Passera | Misslyckas |Passera |
| Nexus (API 28) |Passera | Passera | Passera | Passera | Misslyckas |Passera |
|MI | Passera | Passera | Passera | Passera | Misslyckas |Passera |

* Samsungs inbyggda webbläsare är Samsung Internet.  
** Huaweis inbyggda webbläsare är Huawei Browser.  
Standardwebbläsaren kan inte ändras i Oppo-enhetsinställningen.
