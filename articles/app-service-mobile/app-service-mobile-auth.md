---
title: Autentisering och auktorisering i Azure App Service för mobila appar | Microsoft Docs
description: Konceptuell referens och översikt över autentisering / auktorisering funktion för Azure App Service, särskilt för mobilappar
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
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859132"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autentisering och auktorisering i Azure App Service för appar

Den här artikeln beskrivs hur autentisering och auktorisering fungerar när du utvecklar inbyggda mobilappar med en App Service-serverdel. App Service tillhandahåller integrerad autentisering och auktorisering, så att dina mobila appar kan logga användare utan att ändra någon kod i App Service. Det ger ett enkelt sätt att skydda ditt program och arbeta med data per användare. 

Den här artikeln fokuserar på utveckling av mobilappar. Kom igång snabbt med App Service-autentisering och auktorisering för din mobilapp, ser du något av följande självstudier [Lägg till autentisering i din iOS-app] [ iOS] (eller [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], eller [Cordova]). 

Information om hur autentisering och auktorisering fungerar i App Service finns i [autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autentisering med provider-SDK

När allt har konfigurerats i App Service kan ändra du mobila klienter att logga in med App Service. Det finns två sätt här:

* Använda en SDK som en viss identitetsprovider publicerar om du vill kontrollera identitet och sedan få åtkomst till App Service.
* Använda en enda rad kod så att SDK för Mobile Apps-klient kan logga in användare.

> [!TIP]
> De flesta program ska använda en provider-SDK för att få en mer konsekvent användarupplevelse när användare loggar in, använda tokenuppdatering support och för att få andra förmåner som anger om providern.
> 
> 

När du använder en provider-SDK kan logga användare in till en upplevelse som är integrerat mer med operativsystem som appen körs på. Den här metoden ger dig också en provider-token och viss användarinformation på klienten, vilket gör det enklare att använda graph API: er och anpassa användarupplevelsen. Ibland på bloggar och forum kallas det ”klientflödet” eller ”klient-riktade flöde” eftersom kod på klienten loggar in användare och klientkoden har åtkomst till en provider-token.

När en provider-token hämtas, måste den skickas till App Service för verifiering. När App Service verifierar token, skapar en ny App Service-token som returneras till klienten i App Service. SDK för Mobile Apps-klient har hjälpmetoder för att hantera den här exchange och koppla token till alla begäranden till serverdelen program automatiskt. Utvecklare kan också behålla en referens till en provider-token.

Läs mer på autentiseringsflödet [App Service-autentiseringsflödet](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autentisering utan provider-SDK

Om du inte vill konfigurera en provider-SDK kan du tillåta funktionen Mobile Apps i Azure App Service för att logga in för dig. SDK för Mobile Apps-klient öppnas en webbvy till leverantören av valfritt och loggar in användaren. Ibland på bloggar och forum kallas ”server flöde” eller ”server-riktade flöde” eftersom servern hanterar den process som loggar in användare och klient-SDK får aldrig provider-token.

Kod för att starta det här flödet ingår i självstudiekursen för autentisering för varje plattform. Klient-SDK har en App Service-token i slutet av flödet och token kopplas automatiskt till alla begäranden till serverdelen för programmet.

Läs mer på autentiseringsflödet [App Service-autentiseringsflödet](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Fler resurser

Följande självstudier beskriver hur du lägger till autentisering till dina mobila klienter med hjälp av den [server-riktade flow](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Lägg till autentisering i din iOS-app][iOS]
* [Lägg till autentisering i din Android-app][Android]
* [Lägg till autentisering i din Windows-app][Windows]
* [Lägg till autentisering i ditt Xamarin.iOS-app][Xamarin.iOS]
* [Lägg till autentisering i din Xamarin.Android-app][Xamarin.Android]
* [Lägg till autentisering i din Xamarin.Forms-app][Xamarin.Forms]
* [Lägg till autentisering i din Cordova-app][Cordova]

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Azure Active Directory:

* [Använd Active Directory Authentication Library för iOS][ADAL-iOS]
* [Använda Active Directory Authentication Library för Android][ADAL-Android]
* [Använd Active Directory Authentication Library för Windows- och Xamarin][ADAL-dotnet]

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Facebook:

* [Använda Facebook-SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Twitter:

* [Använd Twitter Fabric för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Använd följande resurser om du vill använda den [klienten dirigeras flödet](../app-service/overview-authentication-authorization.md#authentication-flow) för Google:

* [Använd SDK för Google-inloggning för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
