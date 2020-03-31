---
title: Xamarin Android system webbläsare överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om hur du använder systemwebbläsare på Xamarin Android med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132617"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android system webbläsare överväganden för att använda MSAL.NET

I den här artikeln beskrivs vad du bör tänka på när du använder systembläddraren på Xamarin Android med Microsoft Authentication Library for .NET (MSAL.NET).

Från och med MSAL.NET 2.4.0 Preview stöder MSAL.NET andra webbläsare än Chrome. Det kräver inte längre att Chrome installeras på Android-enheten för autentisering.

Vi rekommenderar att du använder webbläsare som stöder anpassade flikar. Här är några exempel på följande webbläsare:

| Webbläsare som har stöd för anpassade flikar | Paketnamn |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia (av ecosia) | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Modiga | com.brave.browser|

Förutom att identifiera webbläsare som erbjuder stöd för anpassade flikar visar vår testning att några webbläsare som inte stöder anpassade flikar också fungerar för autentisering. Dessa webbläsare inkluderar Opera, Opera Mini, InBrowser och Maxthon. 

## <a name="tested-devices-and-browsers"></a>Testade enheter och webbläsare
I följande tabell visas de enheter och webbläsare som har testats för autentiseringskompatibilitet.

| Enhet | Webbläsare     |  Resultat  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Passera|
| Huawei/One+ | Edge\* | Passera|
| Huawei/One+ | Firefox\* | Passera|
| Huawei/One+ | Modiga\* | Passera|
| En+ | Ecosia (av ecosia)\* | Passera|
| En+ | Kiwi\* | Passera|
| Huawei/One+ | Opera | Passera|
| Huawei | OperaMini (0) | Passera|
| Huawei/One+ | InBrowser (inBrowser) | Passera|
| En+ | Maxthon | Passera|
| Huawei/One+ | Duckduckgo | Användaren avbröt autentiseringen|
| Huawei/One+ | UC Webbläsare | Användaren avbröt autentiseringen|
| En+ | Dolphin | Användaren avbröt autentiseringen|
| En+ | CM Webbläsare | Användaren avbröt autentiseringen|
| Huawei/One+ | Ingen installerad | AndroidActivityNotFound-undantag|

\*Stöder anpassade flikar

## <a name="known-issues"></a>Kända problem

Om användaren inte har någon webbläsare aktiverad på enheten `AndroidActivityNotFound` kommer MSAL.NET att utlösa ett undantag.  
  - **Begränsning**: Be användaren att aktivera en webbläsare på sin enhet. Rekommendera en webbläsare som stöder anpassade flikar.

Om autentiseringen misslyckas (till exempel om autentiseringen startar med `AuthenticationCanceled MsalClientException`DuckDuckGo) returneras MSAL.NET . 
  - **Rotproblem:** En webbläsare som stöder anpassade flikar var inte aktiverad på enheten. Autentisering som lanserades med en webbläsare som inte kunde slutföra autentiseringen. 
  - **Begränsning**: Be användaren att aktivera en webbläsare på sin enhet. Rekommendera en webbläsare som stöder anpassade flikar.

## <a name="next-steps"></a>Nästa steg
Mer information och kodexempel finns i [Välja mellan en inbäddad webbläsare och en systemwebbläsare på Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) och Embedded versus system web [UI](msal-net-web-browsers.md#embedded-vs-system-web-ui).  