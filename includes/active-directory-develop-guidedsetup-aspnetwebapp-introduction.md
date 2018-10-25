---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: bc439cbe5e690077213b5d7953a4e74488988c3b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988556"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till logga in med Microsoft i en ASP.NET-webbapp

Den här guiden visar hur du implementerar inloggning med Microsoft med hjälp av en ASP.NET MVC-lösning med ett traditionellt webbläsarbaserade webbprogram med OpenID Connect.

I slutet av den här guiden kommer programmet att kunna acceptera inloggningar av personliga konton (inklusive outlook.com, live.com och andra) samt arbets- och skolkonton från alla företag eller organisation som har integrerats med Azure Active Directory.

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Saknas det?  [Ladda ned Visual Studio 2017 utan kostnad](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Så här fungerar den här guiden](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Exempelprogrammet som skapats av den här guiden är baserat på scenariot där en användare använder webbläsaren för att komma åt en ASP.NET-webbplats som begär en användare att autentisera via en knapp för inloggning. I det här scenariot sker merparten av renderingen av webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som gör att ett program kan använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som gör att ett program kan hantera en användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Gör att OWIN-baserade program kan köras i IIS med hjälp av pipelinen för ASP.NET-förfrågningar|
