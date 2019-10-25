---
title: Ange en HttpClient och proxy (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tillhandahålla din egen HttpClient och proxy för att ansluta till Azure AD med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259c796cb3653ebdd330f5e65db76cc29c181467
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802771"
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