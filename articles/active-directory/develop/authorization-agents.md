---
title: Auktoriserings agenter och hur du aktiverar dem | Azure
description: Lär dig mer om de olika auktoriserings agenter som Microsoft Authentication Library (MSAL) tillåter att din Android-app använder och hur du aktiverar dem.
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
ms.openlocfilehash: 83a33fa3891e01c484f298f22d67467bc54a7618
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551984"
---
# <a name="authorization-agents-android"></a>Auktoriseringsagenter (Android)

I den här artikeln beskrivs de olika auktoriserings agenter som Microsoft Authentication Library (MSAL) tillåter att din app använder och hur de aktive ras.

Att välja en speciell strategi för auktoriserings agenter är valfritt och representerar ytterligare funktioner som kan anpassas. De flesta appar använder MSAL-standardvärdena (se [förstå konfigurations filen för Android-MSAL](msal-configuration.md) för att se de olika standardvärdena).

MSAL stöder auktorisering med hjälp av en `WebView` eller system webbläsare.  Bilden nedan visar hur den ser ut med hjälp av `WebView` , eller system läsaren med CustomTabs eller utan CustomTabs:

![Exempel på MSAL-inloggning](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Konsekvenser för enkel inloggning

Som standard använder program som är integrerade med MSAL de anpassade flikarna i system webbläsare för att godkänna. Till skillnad från webbvyer delar de anpassade flikarna med standard system webbläsaren och gör färre inloggningar med webb-eller andra inbyggda appar som är integrerade med anpassade flikar.

Om programmet använder en `WebView` strategi utan att integrera Microsoft Authenticator eller företagsportal stöd i sin app, kommer användarna inte att ha någon enkel inloggnings upplevelse på enheten eller mellan inbyggda appar och webbappar.

Om programmet använder MSAL med Microsoft Authenticator-eller Företagsportal-stöd kan användarna ha en SSO-upplevelse över program om användaren har en aktiv inloggning med en av apparna.

## <a name="webview"></a>WebView

Om du vill använda webbvy i appen, Lägg följande rad i JSON-appens konfiguration som skickas till MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

När du använder appen i appen `WebView` loggar användaren in direkt till appen. Token behålls inuti appens Sand låda och är inte tillgängliga utanför appens jar-jar. Det innebär att användaren inte kan ha en SSO-upplevelse över program om inte apparna integreras med autentiseraren eller Företagsportal.

Men `WebView` ger dig möjlighet att anpassa utseendet och känslan för inloggnings gränssnittet. Mer information om hur du gör denna anpassning finns i avsnittet om [Android-webbvyer](https://developer.android.com/reference/android/webkit/WebView) .

## <a name="default-browser-plus-custom-tabs"></a>Standard webbläsare plus anpassade flikar

Som standard använder MSAL webbläsaren och en strategi för [anpassade flikar](https://developer.chrome.com/multidevice/android/customtabs) . Du kan uttryckligen ange den här strategin för att förhindra ändringar i framtida versioner till `DEFAULT` genom att använda följande JSON-konfiguration i den anpassade konfigurations filen:

```json
"authorization_user_agent" : "BROWSER"
```

Använd den här metoden för att tillhandahålla enkel inloggning via enhetens webbläsare. MSAL använder en gemensam cookie-jar, som gör att andra inbyggda appar eller webbappar kan nå SSO på enheten med hjälp av den sparade sessions-cookien som anges av MSAL.

## <a name="browser-selection-heuristic"></a>Heuristiska val av webbläsare

Eftersom det är omöjligt för MSAL att ange det exakta webb läsar paket som ska användas på var och en av de breda Android-telefoner, implementerar MSAL en heuristik för val av webbläsare som försöker tillhandahålla det bästa SSO-värdet mellan enheter.

MSAL hämtar den fullständiga listan över webbläsare som är installerade på enheten för att välja vilken webbläsare som ska användas. Listan är i den ordning som returneras av paket hanteraren, som indirekt återspeglar användarens inställningar. Till exempel är standard webbläsaren, om den är inställd, den första posten i listan. Den _första_ webbläsaren i listan kommer att väljas oavsett om den stöder anpassade flikar eller inte. Om webbläsaren har stöd för anpassade flikar, kommer MSAL att starta den anpassade fliken. anpassade flikar har en känsla som är närmare för en app i appen `WebView` och möjliggör grundläggande anpassning av gränssnittet. Mer information finns i [anpassade flikar i Android](https://developer.chrome.com/multidevice/android/customtabs) .

Om det inte finns några webb läsar paket på enheten använder MSAL i appen `WebView` .

Ordningen på webbläsare i webbläsarens lista bestäms av operativ systemet. Den är i ordning från mest prioriterad till minst. Om enhetens standardinställning inte ändras ska samma webbläsare startas för varje inloggning för att säkerställa en SSO-upplevelse.

> [!NOTE]
> MSAL föredrar inte längre krom om en annan webbläsare är inställd som standard. Till exempel, på en enhet som har både Chrome och en annan webbläsare förinstallerad, kommer MSAL att använda webbläsaren som användaren har angett som standard.

### <a name="tested-browsers"></a>Testade webbläsare

Följande webbläsare har testats för att se om de är korrekt omdirigering till den `"redirect_uri"` angivna i konfigurations filen:

| Enhet | Inbyggd webbläsare | Chrome | Opera  | Microsoft Edge | UC-webbläsare | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |ej tillämpligt |ej tillämpligt |ej tillämpligt |ej tillämpligt |
| Samsung S7 (API 25) | pass | pass | pass | pass | kanske |pass |
| Huawei (API 26) |pass * * | pass | kanske | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|kanske|
| Pixel 2 (API 26) |pass | pass | pass | pass | kanske |pass |
| OPPO | pass | inte tillämpligt * * * |ej tillämpligt  |ej tillämpligt |ej tillämpligt | ej tillämpligt|
| OnePlus (API 25) |pass | pass | pass | pass | kanske |pass |
| Nexus (API 28) |pass | pass | pass | pass | kanske |pass |
|MI | pass | pass | pass | pass | kanske |pass |

* Samsung: s inbyggda webbläsare är Samsung Internet.  
* * Huawei-inbyggda webbläsare är Huawei webbläsare.  
Det går inte att ändra standard webbläsaren i enhets inställningen OPPO.
