---
title: "Utvecklarhandbok för Azure Active Directory | Microsoft Docs"
description: "Den här artikeln innehåller en heltäckande handbok om utvecklarorienterade resurser för Azure Active Directory."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 45ff2ff08cb36f9a4bf49ff04de149accf157e0e
ms.lasthandoff: 03/31/2017


---
# <a name="azure-active-directory-developers-guide"></a>Utvecklarhandbok för Azure Active Directory
## <a name="overview"></a>Översikt
Som en IDMaaS-plattform (IDentity Management as a Service) erbjuder Azure Active Directory (AD) en effektiv lösning med vilken utvecklarna kan integrera identitetshantering i sina program. Följande artiklar innehåller översikter över implementeringen och viktiga funktioner i Azure AD. Vi rekommenderar att du läser dem i ordning eller att du går till [Komma igång](#getting-started) om du är redo att sätta igång.

1. [Fördelarna med Azure AD-integrering](active-directory-how-to-integrate.md): Se varför integrering med Azure AD erbjuder den bästa lösningen för säker inloggning och auktorisering.
2. [Scenarier med Azure AD-autentisering](active-directory-authentication-scenarios.md): Dra nytta av förenklad autentisering i Azure AD för att tillhandahålla inloggning i ditt program.
3. [Integrera program med Azure AD](active-directory-integrating-applications.md): Lär dig hur du lägger till, uppdaterar och tar bort program från Azure AD och få riktlinjer för varumärkesanpassning för integrerade appar.
4. [Microsoft Graph](https://graph.microsoft.io/) och [Azure AD Graph API](active-directory-graph-api.md): För programmässig åtkomst till Azure AD via REST API-slutpunkter. **Vi rekommenderar starkt att du använder Microsoft Graph i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory.** Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.
5. [Azure AD-autentiseringsbibliotek](active-directory-authentication-libraries.md): Autentisera enkelt användare för att erhålla åtkomsttoken med hjälp av Azure AD-autentiseringsbibliotek för .NET, JavaScript, Objective-C och Android och andra.

## <a name="getting-started"></a>Komma igång
Dessa självstudiekurser är skräddarsydda för flera plattformar och hjälper dig att snabbt börja utveckla med Azure Active Directory. Innan du börjar måste du [skaffa en Azure Active Directory-klient](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Snabbstartsguider för mobil- och datorprogram
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | 
|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[NET](active-directory-devquickstarts-dotnet.md) |[Windows</br>Universal](active-directory-devquickstarts-windowsstore.md) |

|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|
|[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integrera direkt</br>med OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Snabbstartsguider för webbappar
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) |
|:---:|:---:|:---:|
| [NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrera direkt</br> med OpenID Connect](active-directory-protocols-openid-connect-code.md) |

| [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|
|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrera direkt</br> med OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Snabbstartsguider för webb-API:er
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="microsoft-graph-and-azure-ad-graph-api-quick-start-guides"></a>Snabbstartsguider för Microsoft Graph och Azure AD Graph API
| [![Microsoft Graph](./media/active-directory-developers-guide/msgraph.png)](https://developer.microsoft.com/graph/quick-start) | [![Azure AD Graph API](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|:---:|
| [Microsoft Graph](https://developer.microsoft.com/graph/quick-start) | [Azure AD Graph API](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Instruktioner
Dessa artiklar beskriver hur du utför specifika aktiviteter med hjälp av Azure Active Directory:

* [Hämta en Azure AD-klientorganisation](active-directory-howto-tenant.md)
* [Logga in för valfri Azure AD-användare med programmönstret för flera klienter](active-directory-devhowto-multi-tenant-overview.md)
* [Använd ett certifikat i stället för en hemlighet för att autentisera en programidentitet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Aktivera enkel inloggning mellan appar med hjälp av ADAL på [Android](active-directory-sso-android.md)- och [iOS](active-directory-sso-ios.md)-enheter
* [AppSource-certifiera ditt program för Azure AD](active-directory-devhowto-appsource-certified.md)
* [Lista ditt program i Azure AD-programgalleriet](active-directory-app-gallery-listing.md)
* [Skicka in webbappar för Office 365 till Seller Dashboard](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Registrera ett program med Azure Active Directory med hjälp av Azure Portal](../active-directory-app-registration.md)
* [Förstå programmanifestet för Azure Active Directory](active-directory-application-manifest.md)
* [Förstå märkningsriktlinjerna för inloggnings- och programanskaffningsknapparna i ditt klientprogram](active-directory-branding-guidelines.md)
* [Förhandsvisning: Skapa program som loggar in användare med både personliga och arbets- eller skolkonton](active-directory-appmodel-v2-overview.md)
* [Förhandsvisning: Skapa appar som registrerar och loggar in konsumenter](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Förhandsvisning: Konfigurera livstid för tokens i AzureAD](../active-directory-configurable-token-lifetimes.md) med PowerShell. Se [Principåtgärder](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) och [Principentiteten](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) för information om konfiguration via Azure AD Graph-API:n.

## <a name="reference"></a>Referens
Dessa artiklar fungerar som en grundläggande referens för REST-API:er och API:er för autentiseringsbibliotek, protokoll, fel, kodexempel och slutpunkter.  

### <a name="support"></a>Support
* [Taggade frågor](http://stackoverflow.com/questions/tagged/azure-active-directory): Hitta Azure Active Directory-lösningar på Stack Overflow genom att söka efter taggarna [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) och [adal](http://stackoverflow.com/questions/tagged/adal).
* Definitioner av några av de vanligast förekommande termerna avseende programutveckling och integrering finns i [Ordlista för Azure AD-utvecklare](active-directory-dev-glossary.md).

### <a name="code"></a>Kod
* [Azure Active Directory-bibliotek med öppen källkod](http://github.com/AzureAD): Det enklaste sättet att hitta ett biblioteks källa är att använda vår [bibliotekslista](active-directory-authentication-libraries.md).
* [Azure Active Directory-exempel](https://github.com/azure-samples?query=active-directory): Det enklaste sättet att navigera i listan med exempel är att använda [indexet över kodexempel](active-directory-code-samples.md).
* [Active Directory Authentication Library (ADAL) för .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) –referensdokumentation finns tillgänglig för både [senaste huvudversion](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) och [tidigare huvudversion](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="microsoft-graph-and-azure-ad-graph-api"></a>Microsoft Graph och Azure AD Graph API
> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://graph.microsoft.io/) i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.
> 

* [Microsoft Graph](https://graph.microsoft.io/): Dokumentation, referens, exempel och SDK: er för Microsoft Graph. 
* [Referens för Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog): REST-referens för Azure Active Directory Graph API. 
* [Behörighetsomfattning för Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): Behörighetsomfattningen för OAuth 2.0 som används för att styra den åtkomst som en app har till katalogdata i en klient.

### <a name="authentication-and-authorization-protocols"></a>Protokoll för autentisering och auktorisering
* [Signeringsnyckelförnyelse i Azure AD](active-directory-signing-key-rollover.md): Läs mer om Azure AD:s signeringsnyckelförnyelsekadens och om hur du uppdaterar nyckeln för de vanligaste programscenarierna.
* [OAuth 2.0-protokollet: Använda auktoriseringskodsbeviljandet](active-directory-protocols-oauth-code.md): Du kan använda OAuth 2.0-protokollets auktoriseringskodsbeviljande om du vill tillåta åtkomst till webbprogram och webb-API:er i din Azure Active Directory-klient.
* [OAuth 2.0-protokollet: Förstå implicit beviljande](active-directory-dev-understanding-oauth2-implicit-grant.md): Läs mer om det implicita auktoriseringsbeviljandet och huruvida det lämpar sig för ditt program.
* [OAuth 2.0-protokollet: Tjänst-till-tjänst-anrop med klientautentiseringsuppgifter](active-directory-protocols-oauth-service-to-service.md): OAuth 2.0 klientautentiseringsuppgiftsbeviljandet referenser tillåter en webbtjänst (en konfidentiell klient) att använda sina egna autentiseringsuppgifter vid anrop till en annan webbtjänst, istället för att personifiera en användare. I det här scenariot är klienten vanligtvis en webbtjänst på mellannivå, en daemon-tjänst eller en webbplats.
* [OpenID Connect 1.0-protokollet: Inloggning och autentisering](active-directory-protocols-openid-connect-code.md): OpenID Connect 1.0-protokollet utökar OAuth 2.0 för användning som ett autentiseringsprotokoll. Ett klientprogram kan ta emot en id_token för att hantera inloggningsprocessen eller utöka auktoriseringskodsflödet till att ta emot både id_token och auktoriseringskod.
* [Referens för SAML 2.0-protokollet](active-directory-saml-protocol-reference.md): SAML 2.0-protokollet gör det möjligt för program att tillhandahålla enkel inloggning för sina användare.
* [WS-Federation 1.2-protokollet](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory har stöd för WS-Federation 1.2 enligt Web Services Federation Version 1.2-specifikationen. Mer information om federationsmetadatadokumentet finns i [Federationsmetadata](active-directory-federation-metadata.md).
* [Typer av token och anspråk som stöds](active-directory-token-and-claims.md): Den här guiden hjälper dig att förstå och utvärdera anspråk i SAML 2.0- och JWT-token (JSON Web).

## <a name="videos"></a>Videoklipp
### <a name="build"></a>Utveckla
Dessa översiktliga presentationer om hur du utvecklar appar med Azure Active Directory ges av talare som arbetar direkt i det tekniska teamet. Presentationerna täcker grundläggande information, inklusive IDMaaS, autentisering, identitetsfederation och enkel inloggning.

* [Microsoft Identity: State of the Union och framtida riktning](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: Identitetshantering som en tjänst för moderna program](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Utveckla moderna webbprogram med Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Utveckla moderna interna program med Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) är en återkommande videoserie som varje fredag erbjuder en kort (10–15 minuter) intervju med experter inom olika Azure-ämnen.  Använd funktionen för tjänstfilter på sidan om du vill visa alla videoklipp om Azure Active Directory.

* [Azure Identity 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Azure Identity 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Azure Identity 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Sociala medier
* [Active Directory-teamets blogg](http://blogs.technet.com/b/ad/): Den senaste utvecklingen inom Azure Active Directory.
* [Azure Active Directory Graph-teamets blogg](https://blogs.msdn.microsoft.com/aadgraphteam): Azure Active Directory-information som är specifik för Graph API.
* [Molnidentitet](http://www.cloudidentity.net): Tankar kring identitetshantering som en tjänst från en framstående Azure Active Directory-programchef.  
* [Azure Active Directory på Twitter](https://twitter.com/azuread): Azure Active Directory-meddelanden på 140 tecken eller mindre.

## <a name="windows-server-on-premises-development"></a>Lokal utveckling med Windows Server
Råd om hur du använder utveckling med Windows Server och Active Directory Federation Services (AD FS) finns i:

* [AD FS-scenarier för utvecklare](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): Innehåller en översikt över AD FS-komponenter och hur de fungerar, med information om autentiserings-/auktoriseringsscenarier som stöds.
* [AD FS-genomgångar](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): En lista över genomgångsartiklar som innehåller stegvisa instruktioner om hur du implementerar relaterade autentiserings-/auktoriseringsflöden.

