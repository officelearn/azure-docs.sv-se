---
title: Autentisering och auktorisering
description: Begreppsmässig referens och översikt över autentiserings-/auktoriseringsfunktionen för Azure App Service, särskilt för mobilappar.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459471"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autentisering och auktorisering i Azure App Service för mobilappar

I den här artikeln beskrivs hur autentisering och auktorisering fungerar när du utvecklar inbyggda mobilappar med en apptjänstbakslut. App Service tillhandahåller integrerad autentisering och auktorisering, så att dina mobilappar kan logga in användare utan att ändra någon kod i App Service. Det är ett enkelt sätt att skydda ditt program och arbeta med data per användare. 

Den här artikeln fokuserar på utveckling av mobilappar. Om du snabbt vill komma igång med apptjänstautentisering och auktorisering för din mobilapp läser du någon av följande självstudier [Lägg till autentisering i din iOS-app][iOS] (eller [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]eller [Cordova]). 

Information om hur autentisering och auktorisering fungerar i App Service finns [i Autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autentisering med provider-SDK

När allt har konfigurerats i App Service kan du ändra mobila klienter så att de loggar in med App Service. Det finns två tillvägagångssätt här:

* Använd en SDK som en viss identitetsprovider publicerar för att fastställa identitet och sedan få åtkomst till App Service.
* Använd en enda kodrad så att Mobile Apps-klienten SDK kan logga in användare.

> [!TIP]
> De flesta program bör använda en provider SDK för att få en mer konsekvent upplevelse när användare loggar in, för att använda tokenuppdateringsstöd och för att få andra fördelar som leverantören anger.
> 
> 

När du använder en provider SDK kan användare logga in på en upplevelse som integreras hårdare med operativsystemet som appen körs på. Den här metoden ger dig också en providertoken och viss användarinformation om klienten, vilket gör det mycket enklare att använda diagram-API:er och anpassa användarupplevelsen. Ibland på bloggar och forum, det kallas "klientflödet" eller "klient-riktade flöde" eftersom koden på klienten tecken i användare, och klientkoden har tillgång till en provider token.

När en providertoken har hämtats måste den skickas till App Service för validering. När App Service har validerat token skapar App Service en ny App Service-token som returneras till klienten. Mobile Apps-klienten SDK har hjälpmetoder för att hantera det här utbytet och automatiskt koppla token till alla begäranden till programmets serverdel. Utvecklare kan också behålla en referens till providertoken.

Mer information om autentiseringsflödet finns i [autentiseringsflödet för App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autentisering utan provider-SDK

Om du inte vill konfigurera en leverantör SDK kan du låta funktionen Mobilappar i Azure App Service logga in åt dig. Mobile Apps-klienten SDK öppnar en webbvy för den leverantör du väljer och loggar in användaren. Ibland på bloggar och forum, kallas det "serverflöde" eller "server-riktat flöde" eftersom servern hanterar den process som tecken i användare, och klienten SDK aldrig tar emot providertoken.

Kod för att starta det här flödet ingår i autentiseringshandledningen för varje plattform. I slutet av flödet har klienten SDK en App Service-token och token kopplas automatiskt till alla begäranden till programmets backend.

Mer information om autentiseringsflödet finns i [autentiseringsflödet för App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Fler resurser

Följande självstudier visar hur du lägger till autentisering till dina mobila klienter med hjälp av [det serverstyrda flödet:](../app-service/overview-authentication-authorization.md#authentication-flow)

* [Lägga till autentisering i din iOS-app][iOS]
* [Lägga till autentisering i din Android-app][Android]
* [Lägga till autentisering i Din Windows-app][Windows]
* [Lägga till autentisering i din Xamarin.iOS-app][Xamarin.iOS]
* [Lägga till autentisering i din Xamarin.Android-app][Xamarin.Android]
* [Lägga till autentisering i din Xamarin.Forms-app][Xamarin.Forms]
* [Lägga till autentisering i din Cordova-app][Cordova]

Använd följande resurser om du vill använda [det klientstyrda flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Azure Active Directory:

* [Använda Active Directory-autentiseringsbiblioteket för iOS][ADAL-iOS]
* [Använda Active Directory-autentiseringsbiblioteket för Android][ADAL-Android]
* [Använda Active Directory-autentiseringsbiblioteket för Windows och Xamarin][ADAL-dotnet]

Använd följande resurser om du vill använda [det klientstyrda flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Facebook:

* [Använda Facebook SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Använd följande resurser om du vill använda [det klientstyrda flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Twitter:

* [Använd Twitter Fabric för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Använd följande resurser om du vill använda [det klientstyrda flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Google:

* [Använda Googles inloggnings-SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
