---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482395"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till inloggning med Microsoft till en ASP.NET-webbapp

Den här guiden visar hur du implementerar inloggning med Microsoft med hjälp av en ASP.NET MVC-lösning med ett traditionellt webbläsarbaserade webbprogram med OpenID Connect.

I slutet av den här guiden kommer programmet att kunna godkänna inloggningar av personliga konton, till exempel outlook.com, live.com och andra. Dessa konton kan du också inkludera arbete och skolkonton från alla företag eller organisation som har integrerats med Azure Active Directory.

> Den här guiden kräver Visual Studio 2019.  Saknas det?  [Ladda ned Visual Studio 2019 utan kostnad](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Visar hur exempelapp som genererats av den här kursen fungerar](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Exempelprogrammet som du skapar baseras på ett scenario där du använder webbläsaren för att få åtkomst till en ASP.NET-webbplats som begär en användare att autentisera via en knapp för inloggning. I det här scenariot sker merparten av renderingen av webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som gör att ett program kan använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som gör att ett program kan hantera en användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Gör att OWIN-baserade program kan köras i IIS med hjälp av pipelinen för ASP.NET-förfrågningar|
