---
title: Xamarin Android system Browser-överväganden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder system webbläsare på Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.openlocfilehash: 8e37d443d75dccae8704bc1ff7856e1d24131766
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652705"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin för Android-systemet för att använda MSAL.NET

I den här artikeln beskrivs vad du bör tänka på när du använder system webbläsaren på Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).

Från och med MSAL.NET 2.4.0 Preview har MSAL.NET stöd för andra webbläsare än Chrome. Det krävs inte längre att Chrome installeras på Android-enheten för autentisering.

Vi rekommenderar att du använder webbläsare som stöder anpassade flikar. Här följer några exempel på dessa webbläsare:

| Webbläsare som har stöd för anpassade flikar | Paketnamn |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwiodlare | com. kiwibrowser. browser|
|Brave | com. Brave. browser|

Förutom att identifiera webbläsare som erbjuder stöd för anpassade flikar, visar vår testning att några webbläsare som inte stöder anpassade flikar fungerar för autentisering. De här webbläsarna är Opera, Opera Mini, inwebbläsare och Maxthon. 

## <a name="tested-devices-and-browsers"></a>Testade enheter och webbläsare
I följande tabell visas de enheter och webbläsare som har testats för autentisering.

| Enhet | Webbläsare     |  Resultat  | 
| ------------- |:-------------:|:-----:|
| Huawei/ett + | Chrome\* | Pass|
| Huawei/ett + | Edge\* | Pass|
| Huawei/ett + | Firefox\* | Pass|
| Huawei/ett + | Brave\* | Pass|
| En och | Ecosia\* | Pass|
| En och | Kiwiodlare\* | Pass|
| Huawei/ett + | Opera | Pass|
| Huawei | OperaMini | Pass|
| Huawei/ett + | Inwebbläsare | Pass|
| En och | Maxthon | Pass|
| Huawei/ett + | DuckDuckGo | Användaren avbröt autentiseringen|
| Huawei/ett + | UC-webbläsare | Användaren avbröt autentiseringen|
| En och | Hos | Användaren avbröt autentiseringen|
| En och | CM-webbläsare | Användaren avbröt autentiseringen|
| Huawei/ett + | Ingen installerad | AndroidActivityNotFound-undantag|

\*Stöder anpassade flikar

## <a name="known-issues"></a>Kända problem

Om användaren inte har någon webbläsare aktive rad på enheten kommer MSAL.NET att utlösa `AndroidActivityNotFound` ett undantag.  
  - **Minskning**: be användaren att aktivera en webbläsare på sina enheter. Rekommendera en webbläsare som stöder anpassade flikar.

Om autentiseringen Miss lyckas (till exempel om autentisering startar med DuckDuckGo) kommer MSAL.NET att `AuthenticationCanceled MsalClientException`returnera. 
  - **Rot problem**: en webbläsare som stöder anpassade flikar har inte Aktiver ATS på enheten. Autentiseringen startades med en webbläsare som inte kunde slutföra autentiseringen. 
  - **Minskning**: be användaren att aktivera en webbläsare på sina enheter. Rekommendera en webbläsare som stöder anpassade flikar.

## <a name="next-steps"></a>Nästa steg
Mer information och kod exempel finns i [välja mellan en inbäddad webbläsare och en system webbläsare på Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) och [Embedded kontra system Web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui).  