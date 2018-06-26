---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 10f5eb239fc6320e7597e5f1380f4df8873ab3b6
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943444"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram

Den här guiden visar hur du implementerar inloggning med Microsoft med hjälp av en ASP.NET MVC-lösning med en traditionell webbläsarbaserad webbapp med OpenID Connect. 

I slutet av den här guiden kommer programmet att kunna acceptera inloggningar av personliga konton (inklusive outlook.com och live.com) samt arbets- och skolkonton från företaget eller organisationen som har integrerat med Azure Active Directory. 

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Har det?  [Hämta Visual Studio 2017 gratis](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar sample-appen som genererats av den här guiden

![Hur den här guiden fungerar](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Det exempelprogram som skapats av den här guiden är baserad på ett scenario där en användare använder webbläsaren för att komma åt en ASP.NET-webbplats som begär en användare autentiseras via en knapp för inloggning. I det här scenariot inträffar mesta av arbetet ska renderas webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som aktiverar ett program att använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som aktiverar ett program att underhålla användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Gör att OWIN-baserade program körs på IIS med hjälp av ASP.NET förfrågnings-pipelinen|

