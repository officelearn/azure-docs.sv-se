---
title: Autentisering och auktorisering i Azure App Service för mobila appar | Microsoft Docs
description: Konceptuell referens- och översikt över autentisering / auktorisering funktion för Azure App Service, särskilt för mobila appar
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 237310c607eb8488e53631b6e69d01703d1ebf99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autentisering och auktorisering i Azure App Service för mobila appar

Den här artikeln beskriver hur autentisering och auktorisering fungerar när du utvecklar interna mobila appar med en App Service-serverdel. App Service tillhandahåller integrerad autentisering och auktorisering, så att dina mobilappar loggar användarna in utan att ändra någon kod i App Service. Det ger ett enkelt sätt att skydda ditt program och arbeta med data per användare. 

Den här artikeln fokuserar på utveckling av mobila appar. Om du vill komma igång snabbt med App Service-autentisering och auktorisering för din mobila app finns i något av följande kurser [Lägg till autentisering i appen för iOS] [ iOS] (eller [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], eller [Cordova ]). 

Mer information om hur autentisering och auktorisering fungerar i App Service finns [autentisering och auktorisering i Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Autentisering med SDK-provider

När allting har konfigurerats i App Service kan ändra du mobila klienter att logga in med App Service. Det finns två tillvägagångssätt här:

* Använd en SDK som en given identitetsleverantör publicerar för att fastställa identitet och få åtkomst till App Service.
* Använd en rad med kod så att Mobile Apps klient-SDK kan logga in användare.

> [!TIP]
> De flesta program ska använda en provider SDK för att få en mer konsekvent användarupplevelse när användare loggar in, att använda token uppdatera support och andra fördelar som providern anger.
> 
> 

När du använder en provider SDK kan logga användare in på en upplevelse som är integrerat mer med operativsystem som programmet körs på. Den här metoden ger dig även en provider-token och viss användarinformation på klienten, vilket gör det enklare att använda graph API: er och anpassa användarupplevelsen. Ibland på bloggar och forum kallas den ”flödet” eller ”klienten dirigeras flödet” eftersom kod på klienten loggar in användare och klienten har åtkomst till en provider-token.

När en provider-token har fått måste den skickas till App Service för verifiering. När Apptjänst validerar token, skapar App Service en ny App Service-token som returneras till klienten. Mobile Apps klient-SDK har helper-metoder för att hantera den här exchange och koppla token till alla förfrågningar till programmet serverdelen automatiskt. Utvecklare kan också ha en referens till en provider-token.

Mer information om autentiseringsflödet finns [Apptjänst autentiseringsflödet](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autentisering utan providern SDK

Om du inte vill konfigurera en provider SDK kan du tillåta funktionen Mobilappar i Azure App Service för att logga in för dig. Mobile Apps klient-SDK öppnar en webbvy till leverantören av du väljer och logga in användaren. Ibland på bloggar och forum kallas ”server flödet” eller ”server-riktade flödet” eftersom servern hanterar processen som loggar in användare och klient-SDK får aldrig provider-token.

Kod för att starta det här flödet ingår i kursen autentisering för varje plattform. Klient-SDK har en Apptjänst-token i slutet av flödet och token kopplas automatiskt till alla förfrågningar till programmet serverdelen.

Mer information om autentiseringsflödet finns [Apptjänst autentiseringsflödet](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>Flera resurser

Följande kurser visar hur du lägger till autentisering i din mobila klienter med den [server-riktade flödet](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Lägg till autentisering i din iOS-app][iOS]
* [Lägg till autentisering i din Android-app][Android]
* [Lägg till autentisering i appen Windows][Windows]
* [Lägg till autentisering i appen Xamarin.iOS][Xamarin.iOS]
* [Lägg till autentisering i appen Xamarin.Android][Xamarin.Android]
* [Lägg till autentisering i appen Xamarin.Forms][Xamarin.Forms]
* [Lägg till autentisering i appen Cordova][Cordova ]

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/app-service-authentication-overview.md#authentication-flow) för Azure Active Directory:

* [Använd Active Directory Authentication Library för iOS][ADAL-iOS]
* [Använd Active Directory Authentication Library för Android][ADAL-Android]
* [Använd Active Directory Authentication Library för Windows och Xamarin][ADAL-dotnet]

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/app-service-authentication-overview.md#authentication-flow) för Facebook:

* [Använd Facebook-SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/app-service-authentication-overview.md#authentication-flow) för Twitter:

* [Använd Twitter infrastruktur för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/app-service-authentication-overview.md#authentication-flow) för Google:

* [Använd SDK för Google-inloggning för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova ]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
