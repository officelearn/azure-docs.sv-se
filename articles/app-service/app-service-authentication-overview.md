---
title: Autentisering och auktorisering i Azure App Service | Microsoft Docs
description: "Konceptuell referens- och översikt över autentisering / auktorisering funktion för Azure App Service"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: f0d2644903181cd2e20166feae4f90ddd4037fa8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service
## <a name="what-is-app-service-authentication--authorization"></a>Vad är App Service autentisering / auktorisering?
App Service autentisering / auktorisering är en funktion som gör det möjligt för programmet att logga in användare så att du inte behöver ändra koden på appens serverdel. Det ger ett enkelt sätt att skydda ditt program och arbeta med data per användare.

Apptjänst använder federerad identitet, där en tredjeparts identitetsprovider lagrar konton och autentiserar användare. Programmet använder leverantörens identitetsinformation så att appen inte behöver lagra informationen sig själv. Apptjänst fem identitetsleverantörer ur lådan: Azure Active Directory, Facebook, Google, Account och Twitter. Din app kan använda valfritt antal dessa identitetsleverantörer för att ge användarna med alternativ för hur de loggar in. Om du vill expandera inbyggt stöd kan du integrera en annan identitetsleverantör eller [egna anpassade identitetslösning][custom-auth].

Om du vill komma igång nu direkt finns i något av följande kurser [Lägg till autentisering i appen för iOS] [ iOS] (eller [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], eller [Cordova]).

## <a name="how-authentication-works-in-app-service"></a>Hur autentisering fungerar i App Service
För att kunna autentisera med någon av identitetsleverantörer först måste du konfigurera identitetsleverantören som du behöver veta om ditt program. Identitetsleverantören sedan ger-ID: N och hemligheter som du anger till App Service. Detta avslutar förtroenderelationen så att Apptjänst kan validera intyg för användaren, till exempel autentiseringstoken från identitetsleverantören.

Om du vill logga in en användare med någon av dessa providers, måste användaren omdirigeras till en slutpunkt som loggar in användare för providern. Om kunder använder en webbläsare måste ha du Apptjänst automatiskt dirigera alla oautentiserade användare som loggar in användare slutpunkten. I annat fall måste du dirigera dina kunder `{your App Service base URL}/.auth/login/<provider>`, där `<provider>` är ett av följande värden: aad, facebook, google, microsoft eller twitter. Mobil- och API-scenarier beskrivs i avsnitten nedan.

Användare som interagerar med ditt program via en webbläsare har en cookie så att de kan vara autentiserade när de surfar ditt program. För andra klienttyper, till exempel mobile, en JSON web token (JWT) som ska visas i den `X-ZUMO-AUTH` sidhuvud, utfärdas till klienten. Mobile Apps klienten SDK hantera det åt dig. Alternativt en token som Azure Active Directory identitet och åtkomst-token kan direkt inkluderas i den `Authorization` huvud som en [ägartoken](https://tools.ietf.org/html/rfc6750).

Apptjänst validerar alla cookie eller token som programmet skickar för att autentisera användare. Om du vill begränsa vem som kan komma åt ditt program, finns det [auktorisering](#authorization) senare i den här artikeln.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Mobila autentisering med en SDK-provider
När allting har konfigurerats på serverdelen kan ändra du mobila klienter att logga in med App Service. Det finns två tillvägagångssätt här:

* Använd en SDK som en given identitetsleverantör publicerar för att fastställa identitet och få åtkomst till App Service.
* Använd en rad med kod så att Mobile Apps klient-SDK kan logga in användare.

> [!TIP]
> De flesta program ska använda en provider SDK för att få en mer konsekvent användarupplevelse när användare loggar in, att använda stöd för uppdatering och andra fördelar som providern anger.
> 
> 

När du använder en provider SDK kan logga användare in på en upplevelse som är integrerat mer med operativsystem som programmet körs på. Detta ger dig också en provider-token och viss användarinformation på klienten, vilket gör det enklare att använda graph API: er och anpassa användarupplevelsen. Ibland på bloggar och forum visas detta som anges som ”klient” eller ”klienten dirigeras flödet” eftersom kod på klienten loggar in användare och klienten har åtkomst till en provider-token.

När en provider-token har fått måste den skickas till App Service för verifiering. När Apptjänst validerar token, skapar App Service en ny App Service-token som returneras till klienten. Mobile Apps klient-SDK har helper-metoder för att hantera den här exchange och koppla token till alla förfrågningar till programmet serverdelen automatiskt. Utvecklare kan också behålla en referens till en provider-token om de vill.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Mobila autentisering utan en SDK-provider
Om du inte vill konfigurera en provider SDK kan du tillåta funktionen Mobilappar i Azure App Service för att logga in för dig. Mobile Apps klient-SDK öppnar en webbvy till leverantören av du väljer och logga in användaren. Ibland på bloggar och forum visas detta kallas ”server flödet” eller ”server-riktade flödet” eftersom servern hanterar processen som loggar in användare och klient-SDK får aldrig provider-token.

Kod för att starta det här flödet ingår i kursen autentisering för varje plattform. Klient-SDK har en Apptjänst-token i slutet av flödet och token kopplas automatiskt till alla förfrågningar till programmet serverdelen.

### <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering
Du kan ge användare åtkomst till ditt program, men du kan också lita ett annat program ska anropa egna API. Du kan till exempel ha en webbapp som en API-anrop i ett annat webbprogram. I det här scenariot använder du autentiseringsuppgifter för ett tjänstkonto i stället för autentiseringsuppgifter för användare för att hämta en token. Ett tjänstkonto kallas även en *tjänstens huvudnamn* i Azure Active Directory kallas och autentisering som använder sådant konto kallas även ett scenario med tjänst-till-tjänst.

> [!IMPORTANT]
> Eftersom mobila appar körs på kunden enheter, mobilprogram gör *inte* räknas som betrodda program och bör inte använda ett service principal flöde. De ska i stället använda ett flöde för användare som har beskrivs ovan.
> 
> 

Scenarier med service to service kan Apptjänst skydda ditt program med hjälp av Azure Active Directory. Det anropande programmet behöver bara ange en Azure Active Directory service principal autentiseringstoken som erhålls genom att ange klientens ID och hemliga från Azure Active Directory-klient. Ett exempel på det här scenariot som använder ASP.NET API apps förklaras i självstudiekursen [huvudnamn autentiseringen av tjänsten för API Apps] [apia tjänst].

Om du vill använda Apptjänst autentisering för att hantera ett scenario med tjänst-till-tjänst kan du använda klientcertifikat eller grundläggande autentisering. Information om klientcertifikat i Azure finns [hur att konfigurera TLS ömsesidig autentisering för Web Apps](app-service-web-configure-tls-mutual-auth.md). Information om grundläggande autentisering i ASP.NET finns [autentisering filter i ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autentiseringen av tjänsten konto från en Apptjänst logic app till en API-app är ett specialfall som beskrivs i [använda anpassat API som finns på Apptjänst med Logic apps](../logic-apps/logic-apps-custom-hosted-api.md).

## <a name="authorization"></a>Så här fungerar auktorisering i Apptjänst
Har du fullständig kontroll över begäranden som har åtkomst till ditt program. App Service autentisering / auktorisering kan konfigureras med något av följande:

* Tillåt endast autentiserade begäranden till ditt program.
  
    Om webbläsaren skickar en begäran om anonym, omdirigerar Apptjänst till en sida för den identitetsleverantör som du väljer så att användarna kan logga in. Om begäran kommer från en mobil enhet, returnerade svaret är en HTTP *401 obehörig* svar.
  
    Med det här alternativet behöver du inte skriva någon Autentiseringskod på alla i din app. Om du behöver bättre auktorisering, är information om användaren tillgänglig för din kod.
* Tillåt alla förfrågningar till når programmet, men Validera autentiserade begäranden och skickar vidare autentiseringsinformation i HTTP-rubriker.
  
    Det här alternativet skiljer sig auktoriseringsbeslut av programkoden. Det ger bättre flexibilitet vid anonyma begäranden, men du måste skriva kod.
* Ingen åtgärd för autentisering på begäran kan alla förfrågningar till programmet.
  
    I detta fall autentisering / auktoriseringsfunktionen är inaktiverad. Uppgifter för autentisering och auktorisering är helt och hållet programkoden.

Tidigare beteenden styrs av den **åtgärd att vidta när begäran inte har autentiserats** alternativet i Azure-portalen. Om du väljer ** logga in med *providernamn* **, alla begäranden som måste autentiseras. **Tillåt begäran (ingen åtgärd)** skjuts upp auktoriseringsbeslut koden, men fortfarande ger autentiseringsinformationen. Om du vill att koden hantera allt, kan du inaktivera autentisering / auktoriseringsfunktionen.

## <a name="working-with-user-identities-in-your-application"></a>Arbeta med användaridentiteter i ditt program
Apptjänst skickar vissa användarinformation till ditt program med hjälp av särskilda huvuden. Externa begäranden förhindrar att dessa huvuden och endast tillgänglig om anges av App Service-autentisering / auktorisering. Vissa exempel huvuden innehåller:

* X-MS-CLIENT--HUVUDNAMN
* X-MS-CLIENT-SÄKERHETSOBJEKT-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Kod som skrivs i valfritt språk eller ramverk kan hämta den information som krävs från dessa huvuden. För ASP.NET 4.6 appar i **ClaimsPrincipal** ställs in automatiskt med lämpliga värden.

Programmet kan också hämta information om ytterligare användare via en HTTP GET på den `/.auth/me` slutpunkten för ditt program. En giltig token som ingår i begäran returneras en JSON-nyttolast med information om den provider som används, den underliggande provider token och vissa andra användarinformation. Mobile Apps server SDK: er ger helper metoder att arbeta med dessa data. Mer information finns i [hur du använder Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), och [arbeta med serverdelen .NET SDK för Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Dokumentation och ytterligare resurser
### <a name="identity-providers"></a>Identitetsleverantörer
Följande kurser visar hur du konfigurerar Apptjänst för att använda olika autentiseringsproviders:

* [Så här konfigurerar du appen för att använda Azure Active Directory-inloggning][AAD]
* [Så här konfigurerar du appen för att använda Facebook-inloggning][Facebook]
* [Så här konfigurerar du appen för att använda Google-inloggning][Google]
* [Så här konfigurerar du appen för att använda Account inloggning][MSA]
* [Så här konfigurerar du appen för att använda Twitter-inloggning][Twitter]

Om du vill använda ett identitetssystem än som anges här, du kan också använda den [Förhandsgranska stöd för anpassad autentisering i Mobile Apps .NET server SDK][custom-auth], som kan användas i webbappar, mobilappar och API apps.

### <a name="mobile-applications"></a>Mobila program
Följande kurser visar hur du lägger till autentisering för mobila klienter med hjälp av server-riktade-flöde:

* [Lägg till autentisering i din iOS-app][iOS]
* [Lägg till autentisering i din Android-app][Android]
* [Lägg till autentisering i appen Windows][Windows]
* [Lägg till autentisering i appen Xamarin.iOS][Xamarin.iOS]
* [Lägg till autentisering i appen Xamarin.Android][Xamarin.Android]
* [Lägg till autentisering i appen Xamarin.Forms][Xamarin.Forms]
* [Lägg till autentisering i appen Cordova][Cordova]

Använd följande resurser om du vill använda klient-riktade flödet för Azure Active Directory:

* [Använd Active Directory Authentication Library för iOS][ADAL-iOS]
* [Använd Active Directory Authentication Library för Android][ADAL-Android]
* [Använd Active Directory Authentication Library för Windows och Xamarin][ADAL-dotnet]

Använd följande resurser om du vill använda klient-riktade flödet för Facebook:

* [Använd Facebook-SDK för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Använd följande resurser om du vill använda klient-riktade flödet för Twitter:

* [Använd Twitter infrastruktur för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Använd följande resurser om du vill använda klient-riktade flödet för Google:

* [Använd SDK för Google-inloggning för iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

<!-- ### API applications
The following tutorials show how to protect your API apps:

* [User authentication for API Apps in Azure App Service][apia-user]
* [Service principal authentication for API Apps in Azure App Service][apia-service] -->

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
