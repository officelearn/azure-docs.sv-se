---
title: Ange en HttpClient & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tillhandahålla din egen HttpClient och proxy för att ansluta till Azure AD med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695064"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Ange din egen HttpClient och proxy med MSAL.NET
När du [initierar ett offentligt klient program](msal-net-initializing-client-applications.md)kan du använda `.WithHttpClientFactory method` för att tillhandahålla egna httpclient.  Genom att tillhandahålla dina egna HttpClient kan du använda avancerade scenarier för att få en begränsad kontroll av en HTTP-proxy, anpassa användar agentens huvuden eller tvinga MSAL att använda en viss HttpClient (till exempel i ASP.NET Core Web Apps/API: er).

## <a name="initialize-with-httpclientfactory"></a>Initiera med HttpClientFactory
I följande exempel visas hur du skapar en `HttpClientFactory` och sedan initierar ett offentligt klient program med det:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient och Xamarin iOS
När du använder Xamarin iOS rekommenderar vi att du skapar en `HttpClient` som uttryckligen använder den `NSURLSession`-baserade hanteraren för iOS 7 och senare. MSAL.NET skapar automatiskt ett `HttpClient` som använder `NSURLSessionHandler` för iOS 7 och senare. Mer information finns i [Xamarin iOS-dokumentationen för httpclient](/xamarin/cross-platform/macios/http-stack).