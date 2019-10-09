---
title: Autentisering och auktorisering i Azure App Service för Mobile Apps | Microsoft Docs
description: Konceptuell referens och översikt över autentiserings-/auktoriserings funktionen för Azure App Service, särskilt för mobila appar
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 3c7329638bd8a5d9e466864d780b7784304fa874
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027489"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autentisering och auktorisering i Azure App Service för mobila appar

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

Den här artikeln beskriver hur autentisering och auktorisering fungerar när du utvecklar interna mobilappar med en App Service server del. App Service ger integrerad autentisering och auktorisering, så att dina mobilappar kan logga in användare i utan att ändra någon kod i App Service. Det är ett enkelt sätt att skydda ditt program och arbeta med data per användare. 

Den här artikeln fokuserar på utveckling av mobilappar. För att komma igång snabbt med App Service autentisering och auktorisering för din mobilapp, se någon av följande själv studie kurser [Lägg till autentisering till din iOS-app][iOS] (eller [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]eller [Cordova]). 

Information om hur autentisering och auktorisering fungerar i App Service finns [i autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autentisering med SDK för Provider

När allt har kon figurer ATS i App Service kan du ändra mobila klienter för inloggning med App Service. Det finns två sätt här:

* Använd ett SDK som en identitets leverantör publicerar för att upprätta identitet och få till gång till App Service.
* Använd en enda kodrad så att klient-SDK: n för Mobile Apps kan logga in användare.

> [!TIP]
> De flesta program bör använda en provider SDK för att få en mer enhetlig upplevelse när användarna loggar in, för att använda stöd för token-uppdatering och för att få andra fördelar som providern anger.
> 
> 

När du använder en provider SDK kan användarna logga in till en upplevelse som integrerar mer tätt med det operativ system som appen körs på. Den här metoden ger dig också en provider-token och viss användar information på klienten, vilket gör det mycket enklare att använda Graph API: er och anpassa användar upplevelsen. Ibland på Bloggar och forum kallas det "klient flöde" eller "klient dirigerat flöde" eftersom kod på klienten loggar in användare och klient koden har åtkomst till en provider-token.

När en provider-token har hämtats måste den skickas till App Service för verifiering. När App Service har verifierat token skapar App Service en ny App Service-token som returneras till klienten. Mobile Apps-klientens SDK har hjälp metoder för att hantera det här Exchange och automatiskt koppla token till alla begär anden till programmets Server del. Utvecklare kan också ha en referens till providerns token.

Mer information om Authentication Flow finns i [App Service Authentication Flow](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autentisering utan Provider-SDK

Om du inte vill konfigurera en SDK för leverantörer kan du tillåta att Mobile Apps funktionen i Azure App Service logga in åt dig. Mobile Apps-klientens SDK öppnar en webbvy till leverantören där du väljer och loggar in användaren. Ibland kallas det "Server Flow" eller "Server-dirigerat flöde" ibland på Bloggar och forum, eftersom servern hanterar processen som loggar in användare, och klient-SDK: n tar aldrig emot providerns token.

Kod för att starta det här flödet ingår i själv studie kursen för autentisering för varje plattform. I slutet av flödet har klient-SDK: n en App Service token, och token ansluts automatiskt till alla begär anden till program Server delen.

Mer information om Authentication Flow finns i [App Service Authentication Flow](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Fler resurser

Följande självstudier visar hur du lägger till autentisering till dina mobila klienter med hjälp av det [serverbaserade flödet](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Lägg till autentisering i din iOS-app][iOS]
* [Lägg till autentisering i din Android-app][Android]
* [Lägg till autentisering i Windows-appen][Windows]
* [Lägg till autentisering i din Xamarin. iOS-app][Xamarin.iOS]
* [Lägg till autentisering i din Xamarin. Android-app][Xamarin.Android]
* [Lägg till autentisering i din Xamarin. Forms-app][Xamarin.Forms]
* [Lägg till autentisering i Cordova-appen][Cordova]

Använd följande resurser om du vill använda det [klient dirigerade flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Azure Active Directory:

* [Använd Active Directory-autentiseringsbibliotek för iOS][ADAL-iOS]
* [Använd Active Directory-autentiseringsbibliotek för Android][ADAL-Android]
* [Använd Active Directory-autentiseringsbibliotek för Windows och Xamarin][ADAL-dotnet]

Använd följande resurser om du vill använda det [klientbaserade flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Facebook:

* [Använd Facebook SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Använd följande resurser om du vill använda det [klient dirigerade flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Twitter:

* [Använd Twitter-infrastruktur för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Använd följande resurser om du vill använda det [klientbaserade flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Google:

* [Använd Google-inloggning SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
