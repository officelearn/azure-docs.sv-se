---
title: Överväganden för Xamarin Android (Microsoft Authentication Library för .NET) | Azure
description: Läs mer om specifika överväganden när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544426"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Överväganden för Xamarin Android-specifika med MSAL.NET
Den här artikeln beskriver specifika överväganden när du använder system-webbläsaren på Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

Från och med MSAL.NET 2.4.0-preview, MSAL.NET har stöd för andra webbläsare än Chrome och kräver inte längre Chrome installeras på Android-enhet för autentisering.

Vi rekommenderar att du använder webbläsare som stöder anpassade tabbar, som följande:

| Webbläsare med stöd för anpassade flikar | Paketnamn |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Modig | com.brave.browser|

Förutom webbläsare med stöd för anpassade flikar, baserat på våra tester fungerar även några webbläsare som inte stöder anpassade flikar för autentisering: Opera, Opera Mini InBrowser och Maxthon. Mer information finns i [tabellen för testresultaten](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Kända problem

- Om användaren har inga webbläsare aktiverad på enheten, MSAL.NET genereras en `AndroidActivityNotFound` undantag. 
  - **Minskning**: Informera användaren om att de ska aktivera en webbläsare (helst en med stöd för anpassade flikar) på sin enhet.

- Om autentiseringen misslyckas (t.ex.) autentisering startar med DuckDuckGo), MSAL.NET returnerar en `AuthenticationCanceled MsalClientException`. 
  - **Rot problemet**: En webbläsare med stöd för anpassade flikar har inte aktiverats på enheten. Autentisering startas med en annan webbläsare som inte går att slutföra autentiseringen. 
  - **Minskning**: Informera användaren om att de ska installera en webbläsare (helst en med stöd för anpassad flik) på sin enhet.

## <a name="devices-and-browsers-tested"></a>Enheter och webbläsare som testas
I följande tabell visas vilka enheter och webbläsare som har testats.

| | Webbläsare&ast;     |  Resultat  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | Skicka|
| Huawei/One+ | Edge&ast; | Skicka|
| Huawei/One+ | Firefox&ast; | Skicka|
| Huawei/One+ | Modig&ast; | Skicka|
| One+ | Ecosia&ast; | Skicka|
| One+ | Kiwi&ast; | Skicka|
| Huawei/One+ | Opera | Skicka|
| Huawei | OperaMini | Skicka|
| Huawei/One+ | InBrowser | Skicka|
| One+ | Maxthon | Skicka|
| Huawei/One+ | DuckDuckGo | Användaren avbröt auth|
| Huawei/One+ | UC webbläsare | Användaren avbröt auth|
| One+ | Dolphin | Användaren avbröt auth|
| One+ | CM-webbläsare | Användaren avbröt auth|
| Huawei/One+ | ingen installerad | AndroidActivityNotFound ex|

&ast; Stöder anpassade flikar

## <a name="next-steps"></a>Nästa steg
Kod kodfragment och ytterligare information om hur du använder system webbläsare med Xamarin Android finns i det här [guide](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  