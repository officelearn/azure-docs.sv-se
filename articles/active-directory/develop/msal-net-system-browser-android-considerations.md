---
title: Xamarin Android-överväganden (Microsoft Authentication Library för .NET) | Azure
description: Lär dig mer om att tänka på när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532571"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-/regionsspecifika överväganden med MSAL.NET
I den här artikeln beskrivs olika saker som du bör tänka på när du använder system läsaren på Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

Från och med MSAL.NET 2.4.0-Preview har MSAL.NET stöd för andra webbläsare än Chrome och kräver inte längre att Chrome installeras på Android-enheten för autentisering.

Vi rekommenderar att du använder webbläsare som stöder anpassade flikar, till exempel följande:

| Webbläsare med stöd för anpassade flikar | Paket namn |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com. Brave. browser|

Förutom webbläsare med stöd för anpassade flikar, baserat på våra tester, kommer några webbläsare som inte stöder anpassade flikar också att fungera för autentisering: Opera, Opera Mini, inwebbläsare och Maxthon. Mer information hittar du [i tabell för test resultat](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Kända problem

- Om användaren inte har någon webbläsare aktive rad på enheten kommer MSAL.net att utlösa `AndroidActivityNotFound` ett undantag. 
  - **Minskning**: Informera användaren om att de bör aktivera en webbläsare (helst en med stöd för anpassade flikar) på sina enheter.

- Om autentiseringen Miss lyckas (t. ex. autentisering startar med DuckDuckGo), MSAL.NET returnerar en `AuthenticationCanceled MsalClientException`. 
  - **Rot problem**: En webbläsare med stöd för anpassade flikar har inte Aktiver ATS på enheten. Autentiseringen startades med en alternativ webbläsare, vilket inte kunde slutföra autentiseringen. 
  - **Minskning**: Informera användaren om att de bör installera en webbläsare (helst en med stöd för anpassade flikar) på sina enheter.

## <a name="devices-and-browsers-tested"></a>Enheter och webbläsare som testats
I följande tabell visas de enheter och webbläsare som har testats.

| | Webbläsare&ast;     |  Resultat  | 
| ------------- |:-------------:|:-----:|
| Huawei/ett + | Chrome&ast; | Godkänd|
| Huawei/ett + | Marginal&ast; | Godkänd|
| Huawei/ett + | Firefox&ast; | Godkänd|
| Huawei/ett + | Brave&ast; | Godkänd|
| En och | Ecosia&ast; | Godkänd|
| En och | Kiwi&ast; | Godkänd|
| Huawei/ett + | Opera | Godkänd|
| Huawei | OperaMini | Godkänd|
| Huawei/ett + | Inwebbläsare | Godkänd|
| En och | Maxthon | Godkänd|
| Huawei/ett + | DuckDuckGo | Användare avbruten autentisering|
| Huawei/ett + | UC-webbläsare | Användare avbruten autentisering|
| En och | Hos | Användare avbruten autentisering|
| En och | CM-webbläsare | Användare avbruten autentisering|
| Huawei/ett + | ingen installerad | AndroidActivityNotFound ex|

&ast;Stöder anpassade flikar

## <a name="next-steps"></a>Nästa steg
Läs den här [guiden](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)för kodfragment och ytterligare information om hur du använder system webbläsare med Xamarin Android.  