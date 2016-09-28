<properties
   pageTitle="Utvecklarhandbok för Azure Active Directory | Microsoft Azure"
   description="Den här artikeln innehåller en heltäckande handbok om utvecklarorienterade resurser för Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="mbaldwin"/>



# Utvecklarhandbok för Azure Active Directory

## Översikt
Som en IDMaaS-plattform (IDentity Management as a Service) erbjuder Azure Active Directory (AD) en effektiv lösning med vilken utvecklarna kan integrera identitetshantering i sina program. Följande artiklar innehåller översikter över implementeringen och viktiga funktioner i Azure AD. Vi rekommenderar att du läser dem i ordning eller att du går till [Komma igång](#getting-started) om du är redo att sätta igång.


1. [Fördelarna med Azure AD-integrering](active-directory-how-to-integrate.md): Se varför integrering med Azure AD erbjuder den bästa lösningen för säker inloggning och auktorisering.

1. [Scenarier med Azure AD-autentisering](active-directory-authentication-scenarios.md): Dra nytta av förenklad autentisering i Azure AD för att tillhandahålla inloggning i ditt program.

1. [Integrera program med Azure AD](active-directory-integrating-applications.md): Lär dig hur du lägger till, uppdaterar och tar bort program från Azure AD och få riktlinjer för varumärkesanpassning för integrerade appar.

1. [Azure AD Graph-API:et](active-directory-graph-api.md): Använd Azure AD Graph-API:et om du vill ha programmässig åtkomst till Azure AD via REST API-slutpunkter. Azure AD Graph-API:et är också tillgänglig via [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph tillhandahåller ett enhetligt API som ger åtkomst till flera av Microsofts molntjänst-API:er genom en enda REST API-slutpunkt, och med en enda åtkomsttoken.

1. [Azure AD-autentiseringsbibliotek](active-directory-authentication-libraries.md): Autentisera enkelt användare för att erhålla åtkomsttoken med hjälp av Azure AD-autentiseringsbibliotek för .NET, JavaScript, Objective-C och Android och andra.


## Komma igång

Dessa självstudiekurser är skräddarsydda för flera plattformar och hjälper dig att snabbt börja utveckla med Azure Active Directory. Innan du börjar måste du [skaffa en Azure Active Directory-klient](active-directory-howto-tenant.md).

### Snabbstartsguider för mobil- och datorprogram

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Universal](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrera direkt med OAuth 2.0](active-directory-protocols-oauth-code.md)|

### Snabbstartsguider för webbappar

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrera direkt med OpenID Connect](active-directory-protocols-openid-connect-code.md)|

### Snabbstartsguider för webb-API:er

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Snabbstartsguide för katalogfrågor

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## Instruktioner

Dessa artiklar beskriver hur du utför specifika aktiviteter med hjälp av Azure Active Directory:

- [Hämta en Azure AD-klient](active-directory-howto-tenant.md)
- [Logga in valfri Azure AD-användare med hjälp av programmönster för flera användare](active-directory-devhowto-multi-tenant-overview.md) 
- Aktivera enkel inloggning mellan appar med hjälp av ADAL på [Android](active-directory-sso-android.md)- och [iOS](active-directory-sso-ios.md)-enheter
- [Gör ditt program AppSource-certifierat för Azure AD](active-directory-devhowto-appsource-certified.md)
- [Lista ditt program i Azure AD-programgalleriet](active-directory-app-gallery-listing.md)
- [Skicka webbappar för Office 365 till instrumentpanelen för försäljning](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Förstå Azure Active Directory-programmanifestet](active-directory-application-manifest.md)
- [Förstå anpassningsriktlinjerna för inloggnings- och programanskaffningsknapparna i klientprogrammet](active-directory-branding-guidelines.md)
- [Förhandstitt: Skapa appar som loggar in användare med både personliga konton och arbets- eller skolkonton](active-directory-appmodel-v2-overview.md)
- [Förhandstitt: Skapa appar som registrerar och loggar in användare](../active-directory-b2c/active-directory-b2c-overview.md)


## Referens

Dessa artiklar fungerar som en grundläggande referens för REST-API:er och API:er för autentiseringsbibliotek, protokoll, fel, kodexempel och slutpunkter.  

###  Support
- [Taggade frågor](http://stackoverflow.com/questions/tagged/azure-active-directory): Hitta Azure Active Directory-lösningar på Stack Overflow genom att söka efter taggarna [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) och [adal](http://stackoverflow.com/questions/tagged/adal).
- Definitioner av några av de vanligast förekommande termerna avseende programutveckling och integrering finns i [Ordlista för Azure AD-utvecklare](active-directory-dev-glossary.md).

### Kod

- [Azure Active Directory-bibliotek med öppen källkod](http://github.com/AzureAD): Det enklaste sättet att hitta ett biblioteks källa är att använda vår [bibliotekslista](active-directory-authentication-libraries.md).

- [Azure Active Directory-exempel](https://github.com/azure-samples?query=active-directory): Det enklaste sättet att navigera i listan med exempel är att använda [indexet över kodexempel](active-directory-code-samples.md).

- [ADAL för .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): Dokumentation för .NET-autentiseringsbiblioteket.

### Graph API

- [Referens för Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): REST-referens för Azure Active Directory Graph API. [Visa den interaktiva miljön för Graph API-referensen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Behörighetsomfattning för Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): Behörighetsomfattningen för OAuth 2.0 som används för att styra åtkomsten som en app har till katalogdata i en klientorganisation.

### Protokoll för autentisering och auktorisering

- [Signeringsnyckelförnyelse i Azure AD](active-directory-signing-key-rollover.md): Läs mer om Azure AD:s signeringsnyckelförnyelsekadens och om hur du uppdaterar nyckeln för de vanligaste programscenarierna.

- [OAuth 2.0-protokollet: Använda auktoriseringskodsbeviljandet](active-directory-protocols-oauth-code.md): Du kan använda OAuth 2.0-protokollets auktoriseringskodsbeviljande om du vill tillåta åtkomst till webbprogram och webb-API:er i din Azure Active Directory-klient.

- [OAuth 2.0-protokollet: Förstå implicit beviljande](active-directory-dev-understanding-oauth2-implicit-grant.md): Läs mer om det implicita auktoriseringsbeviljandet och huruvida det lämpar sig för ditt program.

- [OAuth 2.0-protokollet: Tjänst-till-tjänst-anrop med klientautentiseringsuppgifter](active-directory-protocols-oauth-service-to-service.md): OAuth 2.0 klientautentiseringsuppgiftsbeviljandet referenser tillåter en webbtjänst (en konfidentiell klient) att använda sina egna autentiseringsuppgifter vid anrop till en annan webbtjänst, istället för att personifiera en användare. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemon-tjänst eller en webbplats.

- [OpenID Connect 1.0-protokollet: Inloggning och autentisering](active-directory-protocols-openid-connect-code.md): OpenID Connect 1.0-protokollet utökar OAuth 2.0 för användning som ett autentiseringsprotokoll. Ett klientprogram kan ta emot en id_token för att hantera inloggningsprocessen eller utöka auktoriseringskodsflödet till att ta emot både id_token och auktoriseringskod.

- [Referens för SAML 2.0-protokollet](active-directory-saml-protocol-reference.md): SAML 2.0-protokollet gör det möjligt för program att tillhandahålla enkel inloggning för sina användare.

- [WS-Federation 1.2-protokollet](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory har stöd för WS-Federation 1.2 enligt Web Services Federation Version 1.2-specifikationen. Mer information om federationsmetadatadokumentet finns i [Federationsmetadata](active-directory-federation-metadata.md).

- [Typer av token och anspråk som stöds](active-directory-token-and-claims.md): Den här guiden hjälper dig att förstå och utvärdera anspråk i SAML 2.0- och JWT-token (JSON Web).

## Videoklipp

### Utveckla

Dessa översiktliga presentationer om hur du utvecklar appar med Azure Active Directory ges av talare som arbetar direkt i det tekniska teamet. Presentationerna täcker grundläggande information, inklusive IDMaaS, autentisering, identitetsfederation och enkel inloggning.

- [Microsoft Identity: State of the Union and Future Direction](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Identity management as a service for modern applications](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Develop modern web applications with Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Develop modern native applications with Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) är en återkommande videoserie som varje fredag erbjuder en kort (10–15 minuter) intervju med experter inom olika Azure-ämnen.  Använd funktionen för tjänstfilter på sidan om du vill visa alla videoklipp om Azure Active Directory.

- [Azure Identity 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure Identity 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure Identity 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Sociala medier

- [Active Directory-teamets blogg](http://blogs.technet.com/b/ad/): Den senaste utvecklingen inom Azure Active Directory.

- [Azure Active Directory Graph-teamets blogg](http://blogs.msdn.com/b/aadgraphteam): Azure Active Directory-information som är specifik för Graph API.

- [Molnidentitet](http://www.cloudidentity.net): Tankar kring identitetshantering som en tjänst från en framstående Azure Active Directory-programchef.  

- [Azure Active Directory på Twitter](https://twitter.com/azuread): Azure Active Directory-meddelanden på 140 tecken eller mindre.



<!--HONumber=Sep16_HO3-->


