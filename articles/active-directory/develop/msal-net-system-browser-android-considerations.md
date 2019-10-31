---
title: Xamarin Android system Browser-överväganden (Microsoft Authentication Library för .NET)
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder system webbläsare på Xamarin Android när du använder Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83283cc02c60eb452d0e414c0b21843b48f343b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150358"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Xamarin Android system Browser-överväganden med MSAL.NET

I den här artikeln beskrivs olika saker som du bör tänka på när du använder system läsaren på Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

Från och med MSAL.NET 2.4.0-Preview har MSAL.NET stöd för andra webbläsare än Chrome och kräver inte längre att Chrome installeras på Android-enheten för autentisering.

Vi rekommenderar att du använder webbläsare som stöder anpassade flikar, till exempel följande:

| Webbläsare med stöd för anpassade flikar | Paket namn |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwiodlare | com. kiwibrowser. browser|
|Brave | com. Brave. browser|

Förutom webbläsare med stöd för anpassade flikar, baserat på våra tester, fungerar även några webbläsare som inte stöder anpassade flikar för autentisering: Opera, Opera Mini, inwebbläsare och Maxthon. Mer information hittar du [i tabell för test resultat](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Kända problem

- Om användaren inte har någon webbläsare aktive rad på enheten, kommer MSAL.NET att utlösa ett `AndroidActivityNotFound` undantag. 
  - **Minskning**: informera användaren om att de ska aktivera en webbläsare (helst en med anpassade flikar) på sina enheter.

- Om autentiseringen Miss lyckas (t. ex. autentisering startar med DuckDuckGo) kommer MSAL.NET att returnera en `AuthenticationCanceled MsalClientException`. 
  - **Rot problem**: en webbläsare med stöd för anpassade flikar har inte Aktiver ATS på enheten. Autentiseringen startades med en alternativ webbläsare, vilket inte kunde slutföra autentiseringen. 
  - **Minskning**: informera användaren om att de bör installera en webbläsare (helst en med stöd för anpassade flikar) på sina enheter.

## <a name="devices-and-browsers-tested"></a>Enheter och webbläsare som testats
I följande tabell visas de enheter och webbläsare som har testats.

| | Webbläsare&ast;     |  Resultat  | 
| ------------- |:-------------:|:-----:|
| Huawei/ett + | Chrome-&ast; | Pass|
| Huawei/ett + | Kant&ast; | Pass|
| Huawei/ett + | Firefox-&ast; | Pass|
| Huawei/ett + | Brave&ast; | Pass|
| En och | Ecosia&ast; | Pass|
| En och | Kiwiodlare&ast; | Pass|
| Huawei/ett + | Opera | Pass|
| Huawei | OperaMini | Pass|
| Huawei/ett + | Inwebbläsare | Pass|
| En och | Maxthon | Pass|
| Huawei/ett + | DuckDuckGo | Användare avbruten autentisering|
| Huawei/ett + | UC-webbläsare | Användare avbruten autentisering|
| En och | Hos | Användare avbruten autentisering|
| En och | CM-webbläsare | Användare avbruten autentisering|
| Huawei/ett + | ingen installerad | AndroidActivityNotFound ex|

&ast; stöder anpassade flikar

## <a name="next-steps"></a>Nästa steg
Läs den här [guiden](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)för kodfragment och ytterligare information om hur du använder system webbläsare med Xamarin Android.  