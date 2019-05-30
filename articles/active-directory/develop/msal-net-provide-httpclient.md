---
title: Ange en HttpClient och proxy (MSAL.NET) | Azure
description: Läs mer om att tillhandahålla egna HttpClient och proxy för att ansluta till Azure AD med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ab8a85ecc5649352382469e09d7dfd83a5ddfa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305727"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Att tillhandahålla egna HttpClient och proxy med hjälp av MSAL.NET
När [initierar ett offentliga klientprogram](msal-net-initializing-client-applications.md), du kan använda den `.WithHttpClientFactory method` att tillhandahålla egna HttpClient.  Genom att använda din egen HttpClient kan avancerade scenarier sådana detaljerad kontroll över en HTTP-proxy, anpassa användaren agent rubriker eller tvinga MSAL för att använda en specifik HttpClient (till exempel i ASP.NET Core web apps/API: er).

## <a name="initialize-with-httpclientfactory"></a>Initiera med HttpClientFactory
I följande exempel visas för att skapa en `HttpClientFactory` och initiera ett offentliga klientprogram med den:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient och Xamarin iOS
När du använder Xamarin iOS, rekommenderar vi att du skapar en `HttpClient` som uttryckligen använder den `NSURLSession`-baserade hanterare för iOS 7 och senare. MSAL.NET skapar automatiskt en `HttpClient` som använder `NSURLSessionHandler` för iOS 7 och senare. Mer information finns i [Xamarin iOS-dokumentationen för HttpClient](/xamarin/cross-platform/macios/http-stack).