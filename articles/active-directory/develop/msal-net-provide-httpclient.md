---
title: Ge en HttpClient & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om hur du tillhandahåller din egen HttpClient och proxy för att ansluta till Azure AD med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695064"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Tillhandahålla din egen HttpClient och proxy med hjälp av MSAL.NET
När [du initierar ett offentligt klientprogram](msal-net-initializing-client-applications.md)kan du använda `.WithHttpClientFactory method` för att tillhandahålla din egen HttpClient.  Genom att tillhandahålla din egen HttpClient kan du använda avancerade scenarier, sådan detaljerad kontroll av en HTTP-proxy, anpassa användaragenthuvuden eller tvinga MSAL att använda en specifik HttpClient (till exempel i ASP.NET Core-webbappar/API:er).

## <a name="initialize-with-httpclientfactory"></a>Initiera med HttpClientFactory
I följande exempel visas `HttpClientFactory` för att skapa ett och sedan initiera ett offentligt klientprogram med det:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient och Xamarin iOS
När du använder Xamarin iOS rekommenderas `HttpClient` att skapa `NSURLSession`en som uttryckligen använder den -baserade hanteraren för iOS 7 och nyare. MSAL.NET skapar automatiskt en `HttpClient` som använder `NSURLSessionHandler` för iOS 7 och nyare. Mer information finns i [Xamarin iOS-dokumentationen för HttpClient](/xamarin/cross-platform/macios/http-stack).