---
title: Migrera till MSAL.NET | Azure
description: Läs mer om skillnaderna mellan Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD-Autentiseringsbiblioteket för .NET (ADAL.NET) och hur du migrerar till MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2576121bfc945b90ce8ec0260ea30ec110e14dd8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138834"
---
# <a name="migrating-applications-to-msalnet"></a>Migrerar program till MSAL.NET

Både Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD-Autentiseringsbiblioteket för .NET (ADAL.NET) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Fram tills nu, har de flesta utvecklare arbetat med Azure AD för utvecklare plattform (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton konton) genom att begära token med Azure AD Authentication Library (ADAL). Nu, med hjälp av MSAL.NET, du kan autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton och sociala och lokala konton via Azure AD B2C) via Microsoft identity-plattformen slutpunkt. 

Den här artikeln beskriver hur du väljer mellan Microsoft Authentication Library för .NET (MSAL.NET) och Azure AD-Autentiseringsbiblioteket för .NET (ADAL.NET) och jämför de två biblioteken.  

## <a name="differences-between-adal-and-msal-apps"></a>Skillnader mellan ADAL och MSAL appar
I de flesta fall som du vill använda MSAL.NET och Microsoft identity-plattformen slutpunkt, vilket är den senaste generationen av Microsoft authentication Library. Med hjälp av MSAL.NET, du hämta token för användare logga in på ditt program med Azure AD (arbets- och skolkonton konton), Microsoft (personlig)-konton (MSA) eller Azure AD B2C. 

Om du redan är bekant med Azure AD för utvecklare (v1.0) slutpunkt (och ADAL.NET) kanske du vill läsa [vad är nytt i Microsoft identity-plattformen (v2.0) slutpunkten?](active-directory-v2-compare.md).

Du behöver dock fortfarande använda ADAL.NET om programmet behöver för att logga in användare med tidigare versioner av [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services). Mer information finns i [ADFS stöder](https://aka.ms/msal-net-adfs-support).

Nedan sammanfattas några av skillnaderna mellan ADAL.NET och MSAL.NET ![sida-vid-sida-kod](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-paket och -namnområden

ADAL.NET förbrukas från den [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-paketet. namnområdet som ska användas är `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Att använda MSAL.NET behöver du lägga till den [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet paketera och använda den `Microsoft.Identity.Client` namnområde

### <a name="scopes-not-resources"></a>Scope inte resurser

ADAL.NET skaffar token för *resurser*, men MSAL.NET skaffar token för *scope*. Ett antal MSAL.NET AcquireToken åsidosättningar kräver en parameter med namnet scope (`IEnumerable<string> scopes`). Den här parametern är en enkel lista med strängar som deklarerar önskade behörigheter och resurser som har begärts. Välkända scope är de [Microsoft Graph-scope](/graph/permissions-reference).

Det är också möjligt i MSAL.NET att komma åt v1.0 resurser. Mer information finns i [omfång för ett program med v1.0](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Core-klasser

- ADAL.NET använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) som en representation av anslutningen till säkerhet säkerhetstokentjänst (STS) eller auktorisering servern, via en utfärdare. Däremot MSAL.NET är utformad kring [klientprogram](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Den tillhandahåller två separata klasser: `PublicClientApplication` och `ConfidentialClientApplication`

- Hämta token: ADAL.NET och MSAL.NET har samma authentication-samtal (`AcquireTokenAsync` och `AcquireTokenSilentAsync` för ADAL.NET, och `AqquireTokenInteractive` och `AcquireTokenSilent` i MSAL.NET) men med olika parametrar som krävs. En skillnad är det faktum att, i MSAL.NET, inte längre behöver du skickar in den `ClientID` program i varje AcquireTokenXX-anrop. Verkligen, den `ClientID` anges endast en gång när du skapar den (`IPublicClientApplication` eller `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount inte IUser

ADAL.NET manipuleras användare. Men en användare är en person eller en programvaruagent, men den kan ha/äger/ansvara för ett eller flera konton i Microsoft identity-system (flera Azure AD-konton, Azure AD B2C personliga Microsoft-konton). 

MSAL.NET 2.x nu definierar begreppet konto (via gränssnittet IAccount). Den här stor förändring ger rätt semantik: det faktum att samma användare kan ha flera konton i olika Azure AD-kataloger. MSAL.NET innehåller också bättre information i gäst-scenarier som hemma kontoinformation tillhandahålls.

Läs mer om skillnaderna mellan IUser och IAccount [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Undantag

#### <a name="interaction-required-exceptions"></a>Användaren behöver undantag

MSAL.NET har mer utförlig undantag. När tyst autentisering misslyckas i ADAL proceduren är exempelvis fånga upp undantaget och leta efter den `user_interaction_required` felkod:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Mer information finns i [rekommenderat mönster för att hämta en token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) med ADAL.NET

Med hjälp av MSAL.NET, som du ser `MsalUiRequiredException` enligt beskrivningen i [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Hantering av anspråk utmaning undantag

Anspråk i ADAL.NET, utmaning undantag ska hanteras på följande sätt:

- `AdalClaimChallengeException` är ett undantag (härleda från `AdalServiceException`) utlöses av tjänsten om en resurs kräver mer anspråk från användaren (till exempel två faktorer autentisering). Den `Claims` medlem innehåller vissa JSON-fragment med anspråk som förväntas.
- Fortfarande i ADAL.NET, den offentliga klientprogram som tar emot det här undantaget behöver anropa den `AcquireTokenInteractive` åsidosätta att ha en parameter för anspråk. Den här åsidosättningen av `AcquireTokenInteractive` försöker inte ens att träffa cachen eftersom den inte är nödvändigt. Det beror på att token i cacheminnet inte har rätt anspråk (annars en `AdalClaimChallengeException` skulle inte har genererats). Därför finns inget behov av att titta på cacheminnet. Observera att den `ClaimChallengeException` kan tas emot i en WebAPI gör OBO, medan den `AcquireTokenInteractive` ska anropas i en offentlig klientprogram som anropar den här webb-API.
- Mer information, inklusive exempel finns hantering [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

Anspråk i MSAL.NET, utmaning undantag ska hanteras på följande sätt:

- Den `Claims` exponeras i den `MsalServiceException`.
- Det finns en `.WithClaim(claims)` metod som kan tillämpas på den `AcquireTokenInteractive` builder. 

### <a name="supported-grants"></a>Stöds ger

Inte alla bidrag stöds ännu i MSAL.NET och v2.0-slutpunkten. Följande är en sammanfattning jämföra ADAL.NET och MSAL. NET stöds bidrag.

#### <a name="public-client-applications"></a>Offentliga klientprogram

Här är de bidrag som stöds i ADAL.NET och MSAL.NET för Desktop och Mobile-program

Bevilja | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktiv | [Interaktiv autentisering](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Hämta token interaktivt i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrerad Windows-autentisering | [Integrerad autentisering på Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication)
Användarnamn / lösenord | [Hämta token med användarnamn och lösenord](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autentisering med användarnamn lösenord](msal-authentication-flows.md#usernamepassword)
Enhetskodflöde | [Enhetsprofil för enheter utan webbläsare](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Kodflöde för enhet](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Konfidentiellt klientprogram

Här är de bidrag som stöds i ADAL.NET och MSAL.NET för webb-, webb-API: er och daemon-program:

Typen av App | Bevilja | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web App, Web API, daemon | Klientautentiseringsuppgifter | [Autentiseringsuppgifter för klienten som flödar in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Autentiseringsuppgifter för klienten som flödar in MSAL.NET](msal-authentication-flows.md#client-credentials))
Webb-API | På uppdrag av | [Tjänst till tjänst-anrop åt användaren med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [På uppdrag av i MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webbapp | Auth-kod | [Hämta token med auktoriseringskoder på web apps med ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Hämta token med auktoriseringskoder på web apps med en MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cachepersistence

ADAL.NET kan du utöka den `TokenCache` klassen för att implementera önskad persistence funktionerna på plattformar utan en säker lagring (.NET Framework och .NET core) med hjälp av den `BeforeAccess`, och `BeforeWrite` metoder. Mer information finns i [Token Cache-serialisering i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET gör tokens cacheminne en fast klass, tar bort möjligheten att utöka den. Implementeringen av token cachepersistence måste därför vara i form av en hjälparklass som interagerar med förseglade token-cache. Det här beskrivs i [Token Cache-serialisering i MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Sådana betydande av vanliga behörighet

I v1.0, om du använder den https://login.microsoftonline.com/common utfärdaren av som du vill tillåta användare att logga in med ett AAD-konto (för alla organisationer). Se [utfärdare validering i ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Om du använder den https://login.microsoftonline.com/common utfärdare i v2.0 som du vill tillåta användare att logga in med en AAD-organisation eller ett personligt Microsoft-konto (MSA). I MSAL.NET, om du vill begränsa inloggning till alla AAD-konto (samma beteende som med ADAL.NET), måste du använda https://login.microsoftonline.com/organizations. Mer information finns i `authority` parameter i [offentliga klientprogram](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>V1.0 och v2.0-token

Det finns två versioner av token:
- V1.0 token
- V2.0-token 

V1.0 slutpunkten (används av ADAL) genererar endast v1.0 token.

V2.0-slutpunkten (används av MSAL) genererar dock versionen av den token som accepterar webb-API. En egenskap för applikationsmanifestet av webb-API ger utvecklare möjlighet att välja vilken version av token accepteras. Se `accessTokenAcceptedVersion` i den [programmanifestet](reference-app-manifest.md) referensdokumentation.

Läs mer om v1.0 och v2.0-token, [åtkomsttoken för Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Omfång för ett webb-API som tar emot v1.0 token

OAuth2-behörigheter är behörighetsomfattningar som ett v1.0 webb-API (resurs)-program som exponerar för klientprogram. De här behörighetsomfattningarna kan ges klientprogram under medgivande. Finns i avsnittet om oauth2Permissions i [Azure Active Directorys programmanifest](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Sökomfång för att begära åtkomst till specifika OAuth2-behörigheter i ett v1.0-program

Om du vill hämta token för specifika omfång i ett v1.0-program (till exempel AAD-diagrammet, vilket är https://graph.windows.net), skulle du behöva skapa `scopes` genom att sammanfoga en önskad resurs-ID med en önskad OAuth2-behörighet för den resursen.

Till exempel att komma åt namnet på användaren v1.0 webb-API som App-ID-URI är `ResourceId`, du kan använda:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Om du vill läsa och skriva MSAL.NET Azure Active Directory med hjälp av AAD graph API (https://graph.windows.net/), skapar du en lista med omfattningar som i följande kodavsnitt:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Varning: Du om har en eller två snedstreck i omfånget för en v1.0 webb-API

Om du vill skriva området för Azure Resource Manager-API (https://management.core.windows.net/), du måste begära följande omfattningen (Observera två snedstreck) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Detta beror på att Resource Manager API: et förväntar sig ett snedstreck i sin publik-begäran (`aud`), och det finns ett snedstreck att separera API-namn från omfånget.

Den logik som används av Azure AD är följande:
- För ADAL (v1.0) slutpunkten med en v1.0 åtkomsttoken (endast möjliga), aud = resurs
- För MSAL (v2.0-slutpunkt) frågar en åtkomsttoken för en resurs som tar emot v2.0 token, aud = resurs. AppId
- För MSAL (v2.0-slutpunkt) frågar en åtkomsttoken för en resurs som tar emot en token för åtkomst av v1.0 (vilket är fallet ovan), tolkar målgrupp från det begärda omfånget genom att ta allt innan det senaste snedstrecket och använda det som resurs-ID i Azure AD. Därför om https://database.windows.net förväntar sig en målgrupp ”https://database.windows.net/”, måste du begära en omfattning https://database.windows.net//.default. Se också utfärda #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Resurs-url avslutande snedstreck utelämnas, vilket orsakade sql auth fel #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Sökomfång för att begära åtkomst till alla behörigheter för ett v1.0-program

Till exempel om du vill hämta en token för alla statiska omfång i ett v1.0-program skulle en behöva använda

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Sökomfång för att begära när det gäller klienten autentiseringsuppgifter flow / daemon-app

När det gäller klientflödet autentiseringsuppgifter, omfattning att skicka också skulle vara `/.default`. Detta talar om att Azure AD: ”alla appnivå behörigheter som administratören har godkänt att i program-registreringen.

## <a name="adal-to-msal-migration"></a>ADAL för migrering av MSAL

I ADAL.NET v2. X, uppdatera token var tillgängliga så att du kan utveckla lösningar kring användning av dessa token genom att cachelagra dem och använda den `AcquireTokenByRefreshToken` metoder som tillhandahålls av ADAL 2.x. Vissa av dessa lösningar har använts i scenarier som:
* Långvariga tjänster som gör åtgärder inklusive uppdatera instrumentpaneler åt användarna medan användarna är inte längre ansluten. 
* WebFarm scenarier för att aktivera klienten för att få RT till webbtjänsten (cachelagring görs på klientsidan och krypterad cookie inte serversidan)

Detta är inte vad gäller MSAL.NET, men eftersom vi inga längre rekommenderas att använda uppdatera token på detta sätt av säkerhetsskäl. Detta gör det svårt att migrera till MSAL 3.x som API: et tillhandahåller inte ett sätt att skicka in har tidigare hämtat uppdateringstoken. 

Som tur är kan MSAL.NET nu har ett API som gör det möjligt att migrera dina tidigare uppdateringstoken till den `IConfidentialClientApplication` 

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Med den här metoden kan ange du den tidigare uppdateringstoken tillsammans med eventuella scope (resurser) som du önskar. Uppdateringstoken kommer bytas ut för ett nytt lösenord och cachelagras i ditt program.  

Eftersom den här metoden är avsedd för scenarier som inte är typiska, är det inte allmänt tillgänglig med den `IConfidentialClientApplication` utan att först instruktionsvideor den till `IByRefreshToken`.

Det här kodstycket visar kod migrering i ett konfidentiellt klientprogram. `GetCachedRefreshTokenForSignedInUser` Hämta den uppdateringstoken som lagrats i vissa lagring med en tidigare version av det program som används för att utnyttja ADAL 2.x. `GetTokenCacheForSignedInUser` deserializes en cache för den inloggade användaren (som konfidentiellt klientprogram ska ha en cache per användare).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Du ser en åtkomsttoken och ID-token som returneras i din AuthenticationResult medan ditt nya uppdateringstoken lagras i cacheminnet.

Du kan också använda den här metoden för olika integrationsscenarier där du har en uppdateringstoken som är tillgängliga.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om scope i [omfattningar, behörigheter och godkännande i Microsoft identity-plattformen slutpunkten](v2-permissions-and-consent.md)